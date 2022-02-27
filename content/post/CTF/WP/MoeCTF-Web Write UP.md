---
author: "Yiming Shen"
date: 2021-08-19
lastmod: 2021-12-11
title: "MoeCTF 2021 西电新生赛WP"
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

### 前言-2021/8/10

今天学长在群里发了西电的新生CTF比赛，题目较为基础，适合我们初学者。

想来还是感慨颇多的，西电不愧是网安的头部院校，信安的新生还没入学就开始打CTF比赛了，我大二了才刚刚踏入大门而已。

## 1. Web安全入门指北-GET

```php
<?php
include "flag.php";
$moe = $_GET['moe'];
if ($moe == "flag") {
    echo $flag;
}else {
    highlight_file(__FILE__);
}
```

签到题。已给出源代码，根据题目提示，可以看出是一个简单的get命令题。

在地址栏中发送get请求：

`http://47.93.215.154:10000/?moe=flag`

得到flag：

`moectf{We1c0me_t0_CTF_Web!}`

## 2. Web安全入门指北-POST

```php
<?php
include "flag.php";
$moe = $_POST['moe'];
if ($moe == "flag") {
    echo $flag;
}else {
    highlight_file(__FILE__);
}
```

给出源代码，只需要发送post请求给moe变量赋值flag即可。

用hackbar：

在post参数里填入`moe=flag`

Execute执行即可得flag:`moectf{POST_1s_an_1mp0rtant_m3th0d!}`

## 3. Web安全入门指北-小饼干

这道题非常有意思，通过网页提示:

`You are not VIP,I will give flag to VIP!`

可以大致猜测要伪造某些访问项目，将非VIP改为VIP。

用Burp Suite抓包得到，有一个cookie项为VIP=0，可以通过篡改VIP值来达到伪造cookies的目的。

将抓包结果发送到重发器，将VIP值改为1，点击发送。

得到flag。

## 4. 2048

打开环境，发现是一个2048小游戏

先玩一下，失败后提示要达到50000分以后才能得到flag。

试一下盲加get payload：`/?score=60000`

无任何效果，说明负载点选则的不对，抓包看一下在游戏失败后会发送什么数据。

顺便一提，我瞎按的分数都比认真玩的分高。

失败后，成功抓到包：



可以看到，原来是在`flag.php`页面下进行score的get请求。

既然是get请求，那么直接在浏览器地址栏手动输入即可。

`/flag.php?score=60000`



得到flag。

## 5. Web_Inc

这个题卡了很长时间。根据题目提示，是一道MD5绕过题。

```php
<?php
error_reporting(0);
$a=$_GET['a'];
$b=$_POST['b'];
if(isset($a)){
    if($a!=$b&&md5($a)===md5($b)){
        include_once "flag";
    }else{
        echo 'try again';
    }
}else{
    highlight_file(__FILE__);
}
```

题目给出了源代码，据此可以得出，我们需要提供一个get方法变量a和一个post方法变量b，a的值不能等于b的值，但a的md5要等于b的md5。

查阅网上资料得知，PHP在处理哈希字符串时，它把每一个以“0E”开头的哈希值都按照科学计数法处理为0，如此0=0，我们只需要找到两个md5值为0e开头的字符串即可。

>QNKCDZO
>
>240610708
>
>s878926199a
>
>s155964671as
>
>214587387a
>
>s214587387a

以上几组数均为一次md5结果为0e开头的字符串，得到这个信息后，我开开心心的打开hackbar传入一组get参数和post参数：

![image-20210810220645848](C:\Users\Seuil\AppData\Roaming\Typora\typora-user-images\image-20210810220645848.png)

尝试过多组解，甚至改用BP intruder进行爆破，仍然是冷冰冰的try again。

无奈，回头仔细观察源代码：

`md5($a)===md5($b)`

这一项有三个等于号！

上网查询，发现PHP中`===`意味着只有类型相同才会进行比较，否则直接返回false，掐断了使用md5为0e的字符串绕过的可能性！

再查询发现，PHP中的md5函数有漏洞，无法处理数组，遇到数组会报错，但程序能够继续执行。

故传入a[]=1,b[]=2即可。

![image-20210810222139367](C:\Users\Seuil\AppData\Roaming\Typora\typora-user-images\image-20210810222139367.png)

果然，try again的字样消失了，那么flag在哪呢？

点sources查看一下源代码：

![image-20210810222237029](C:\Users\Seuil\AppData\Roaming\Typora\typora-user-images\image-20210810222237029.png)

flag藏在注释里，成功！

### 强类型绕过

使用全等于判断时，若数组无效，直接构造两组答案不同但md5相同的payload即可。

> PHP强类型绕过
>
> ``` 
> a=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2
> &b=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%02%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%d5%5d%83%60%fb%5f%07%fe%a2
> ```

## 6. ezinclude

```php
<?php
error_reporting(0);
if(isset($_GET['file'])){
    $file = $_GET['file'];
    include($file);
}else{
    highlight_file(__FILE__);
} 
```

文件包含，使用了include函数。

先使用`PHP://input`流，可以访问请求的原始数据的只读流，将post请求的数据当作php代码执行。

post代码：

```php
<?php phpinfo(); ?>
```

无效果，怀疑过滤了`PHP://input`，尝试`PHP://filter`，可以获取指定文件源码。当它与包含函数结合时，`php://filter`流会被当作php文件执行。

get一下：`?flie=php://filter/read=convert.base64-encode/resource=flag.php`

但很多时候信息不会直接显示在响应页面中，可以将文件内容进行base64编码再读取，然后再自行解码：

![image-20210810232516100](C:\Users\Seuil\AppData\Roaming\Typora\typora-user-images\image-20210810232516100.png)

base64解码后即得到flag:

`flag = moectf{Do_y0u_l1k3_Rcccccccccccccce?}`

## 7. Do you know HTTP?

这是一道非常有意思的HTTP请求头伪造题，一步一步。

![image-20210810233151625](C:\Users\Seuil\AppData\Roaming\Typora\typora-user-images\image-20210810233151625.png)

根据题意，BP抓包后发给Repeater重发器，修改请求头参数为HS

![image-20210810233314168](C:\Users\Seuil\AppData\Roaming\Typora\typora-user-images\image-20210810233314168.png)

发送后发现，需要本地ip才可以访问。

增加XFF项，修改值为127.0.0.1，成功

![image-20210810233755436](/12.MoeCTF-Web%20Write%20UP.assets/image-20210810233755436.png)

接下来需要将网页来源修改为www.ltyyds.com

增加referer项，值修改为www.ltyyds.com，成功

![image-20210810233943858](/12.MoeCTF-Web%20Write%20UP.assets/image-20210810233943858.png)

浏览器需要LT？在User-Agent项中，将值清空后改为LT。

![image-20210810234119130](/12.MoeCTF-Web%20Write%20UP.assets/image-20210810234119130.png)

成功得到flag！

## 8.地狱通信???



## 9.babeRCE

```php
<?php

$rce = $_GET['rce'];
if (isset($rce)) {
    if (!preg_match("/cat|more|less|head|tac|tail|nl|od|vi|vim|sort|flag| |\;|[0-9]|\*|\`|\%|\>|\<|\'|\"/i", $rce)) {
        system($rce);
    }else {
        echo "hhhhhhacker!!!"."\n";
    }
} else {
    highlight_file(__FILE__);
}
```

源码如图，作为一道RCE（远程命令执行）题，可以说过滤了相当多命令，把命令注入限死在一个很小的范围内，其中，几乎所有查看文件的命令都被过滤了，还把空格过滤了……

好在，没有过滤转义反斜杠和ls/dir等查看目录命令

先get传一个ls命令：

![image-20210810235348697](/12.MoeCTF-Web%20Write%20UP.assets/image-20210810235348697.png)

那么，问题就简化了，我们只需要绕过过滤掉的无数命令，试图读取`flag.php`即可。

上网搜一下Linux还剩哪些命令：`file -f`

该命令在查询出错后会返回文件内容！

那么，空格怎么替换呢？有`${IFS}`可以当空格使

战战兢兢的对照了下，没有被过滤掉的非法字，拼一个命令试试：

`/?rce=file${IFS}-f${IFS}fla\g.php`

进入地址栏……复制粘贴……回车……没有出现返回内容！

成功了？右键-查看源代码，果然！

![image-20210810235946744](/12.MoeCTF-Web%20Write%20UP.assets/image-20210810235946744.png)

得到flag！AC！

### 参考

[1] [CTF中常见php-MD5()函数漏洞](https://blog.csdn.net/qq_19980431/article/details/83018232?utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.pc_relevant_baidujshouduan&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.pc_relevant_baidujshouduan)

[2] [CTF中文件包含漏洞总结](https://blog.csdn.net/qq_42181428/article/details/87090539)

[3] [CTF之HAHA浏览器修改](https://www.cnblogs.com/jasy/p/13473129.html)

[4] [HTTP头伪造](https://www.pianshen.com/article/99021982896/)

[5] [ctfshow命令执行[50]与常见过滤的思考](https://blog.csdn.net/weixin_39667041/article/details/116165436)

[6] [MD5绕过各种姿势](https://blog.csdn.net/CSDNiamcoming/article/details/108837347)

