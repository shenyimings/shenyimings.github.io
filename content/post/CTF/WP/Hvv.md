---
author: "Yiming Shen"
date: 2022-03-11
lastmod: 2022-03-11
title: "护网面经"
tags: [
    "CTF",
    "面试",
    "Web",
    "Note",
]
categories: [
    "Notes",
     "CTF",
]
math: true
showtoc: true
tocopen: true
typora-root-url: ..\..\..\..\static\
---

##  Readme

> 第一次参与hvv，投了三份简历，收集并准备一下常见问题。



## OWASP Top10

![img](/Hvv.assets/%7D/19e123126a352dac5a0d21ab529b2202.png)

**A01 :越权访问（2021-Broken Access Control）**

> 从2017年的第5位上升至第1位。超过94%的app都经历过某种形式的越权访问控制测试。对应到越权访问有34个CWE，比任何其它在app中出现的主题次数都多。



**A02：加密失败（2021-Cryptographic Failures ）**

> 较2017年相比上升1位至第2位。以前被称为敏感数据公开（Sensitive Data Exposure），但只是一种基本症状表现，并不是根本原因。最新版OWASP重新聚焦于与密码学相关的缺陷，这些缺陷通常会导致敏感数据公开或系统受损。



**A03：注入（2021-Injection）**

> 较2017版相比下滑至第3位。超过94% 的app都针对各种形式的注入进行了测试。对应到注入有33 个CWE，在app中出现的次数位列第二。跨站脚本攻击（XSS）目前属于现版本中注入的一部分。



**A04：不安全设计（2021-Insecure Design）**

> 2021年Top 10的新主题，重点关注了与设计缺陷相关的风险。如果我们真的想作为一个行业发展，就需要更多地使用威胁模型分析、安全设计模式和原则以及参考架构。



**A05：安全性错误配置**

**（2021-Security Misconfiguration）**

> 较前版的第6位相比上升1位。90% 的app都需要经过某种形式的错误配置测试。随着更多转向高度可配置的软件，也就不奇怪为什么这个主题排名能够上升了。之前的XXE主题现在也属于A05类别。



**A06：易受攻击与过时组件**

**（2021-Vulnerable and Outdated Components）**

> 前版名称是“应用已知漏洞组件”（Known Vulnerabilities），在行业调查中位列第2，并有足够的数据通过数据分析进入Top 10排名。该类别从 2017 年的第9位上升，是一个难以测试和评估风险的已知问题。这是唯一没有任何CVE可以对应到已归结CWE的主题，因此以默认的利用和影响权重5.0计入评分标准。



**A07：身份验证与认证失败**

**（2021-Identification and Authentication Failures）**

> 以前称为错误认证（Broken Authentication），从第2位下滑至第7位。现在包括与识别失败更多相关的 CWE。有着标准化框架可用性增加的帮助，该主题仍然是前10的一个组成部分。



**A08：软件和数据完整性故障**

**（2021-Software and Data Integrity Failures）**

> 2021 年的一个新主题，着眼于在不验证完整性的情况下，做出与软件更新、关键数据和 CI/CD 管道相关的假设。CVE/CVSS 数据中最高加权影响之一可以对应到A08中的10个CWE。2017年的不安全反序列化（Insecure Deserialization） 现属于A08的一部分。



**A09：安全日志记录和监控失败**

**（2021-Security Logging and Monitoring Failures）**

> 以前被称为日志记录和监控不足（ Insufficient Logging &Monitoring ）。是从行业调查第3位中添加的，从之前的第10位上升。A09被扩大成为一个能够包含更多故障类型的主题，对于我们进行测试有一定的挑战性，而且在 CVE/CVSS 数据中也没有很好的表现。但是，A09类故障会直接影响到可见（visibility）、事件警报（incident alerting）和取证（forensics）的准确性。



**A10：服务器端请求伪造SSRF**

**（2021-Server-Side Request Forgery）**

> A10是直接从行业调查第1位中添加的。数据显示，在高于平均水平的测试里，A10的发生率相对较低，但Exploit和潜在的Impact都高于平均水平。这也正表示了行业专业人士在告诉我们，就算目前数据中没有显示出来，服务器请求伪造还是很重要的事实。

## 题目

### 中间件漏洞

#### IIS

1. 解析漏洞

   > **IIS 6.0在处理含有特殊符号的文件路径时会出现逻辑错误，从而造成文件解析漏洞。**
   >
   > 1. 新建名为`test.asp`的目录，该目录下任何文件都被IIS当作asp程序执行
   > 2. 上传`test.asp;.jpg`的文件，由于含有分号，IIS服务器从头查找，遇分号截断，仍会作为asp程序解析。
   >
   > **漏洞修复**
   >
   > 1. 对新建目录文件名进行过滤，不允许新建含`.`的目录（不允许新建目录）
   > 2. 过滤`.asp;.*`文件（ISAPI组件过滤）
   > 3. 限制上传的文件的执行权限，不允许执行脚本。

2. PUT漏洞

	> **IIS Server在Web服务扩展中开启了WebDAV，配置了可以写入的权限，造成任意文件上传。**
	>
	> **漏洞修复**
	>
	> 关闭WebDAV和写权限。
	
3. 短文件名猜解

  > **IIS的短文件名机制，可以暴力猜解短文件名，访问构造的某个存在的短文件名，会返回404，访问构造的某个不存在的短文件名，返回400。**
  >
  > **漏洞修复**
  >
  > 1. 升级.net Framework
  > 2. 修改注册表禁止短文件名功能
  > 3. 关闭NTFS 8.3 文件格式的支持

4. 远程代码执行

  > **在IIS6.0处理PROPFIND指令的时候，由于对url的长度没有进行有效的长度控制和检查，导致执行memcpy对虚拟路径进行构造的时候，引发栈溢出，从而导致远程代码执行。**
  >
  > **漏洞修复**
  >
  > 1. 关闭WebDAV服务；
  >
  > 2. 使用相关防护设备；

#### Nginx

> 目录遍历、CRLF截断造成cookie或xss注入、文件解析、**目录穿越漏洞**(未用/闭合，url输入../可造成目录穿越)

#### Apache

1. log4j漏洞

   > **log4j中的lookup函数+JNDI导致**
   >
   > lookup函数会执行${}内的命令，再利用JNDI嵌套访问远程服务器，可获取当前服务器信息并造成远程命令执行。
   >
   > **漏洞修复**
   >
   > 1. 禁用log4j或lookup
   > 2. 更新Apache

2. 解析漏洞

3. 目录遍历

#### Tomcat

1. 远程代码执行

   > Tomcat 运行在 Windows 主机上，且启用了 HTTP PUT 请求方法，可通过构造的攻击请求向服务器上传包含任意代码的 JSP 文件，造成任意代码执行。

2. 后台管理webshell(war包)弱口令

3. 反序列化漏洞

### PHP

#### 代码执行RCE

[^ PHP代码执行](https://3wapp.github.io/WebSecurity/php_cmdinject.html)

> ```php
> eval()
>
> assert()
>
> call_user_func()
>
> call_user_func_array()
>
> create_function()
>
> array_map()```

#### 文件包含

```php
require         # 找不到被包含的文件时会产生致命错误，并停止脚本运行。
include         # 找不到被包含的文件时只会产生警告，脚本将继续运行。
include_once    # 与include类似，唯一区别是如果该文件中的代码已经被包含，则不会再次包含。
require_once    # 与require类似，唯一区别是如果该文件中的代码已经被包含，则不会再次包含
```

#### 文件读取函数

```php
file_get_contents()
highlight_file()
fopen()
read file()
fread()
fgetss()
fgets()
parse_ini_file()
show_source()
file()
```



### 正向代理和反向代理

> **正向代理**，当客户端无法访问外部资源的时候（比如Google、YouTube），可以通过一个正向代理去间接地访问。
> 正向代理是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。
>
> ```text
> 正向代理即是客户端代理, 代理客户端, 服务端不知道实际发起请求的客户端
> ```
>
> **反向代理**，客户端是无感知代理的存在，以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端。此时代理服务器对外就表现为一个服务器
>
> ```text
> 反向代理即是服务端代理, 代理服务端, 客户端不知道实际提供服务的服务端
> ```

### 应急响应流程

![img](/Hvv.assets/%7D/v2-900b982d6c846ad6c23f01a97d39c748_b.jpg)

> 响应-阻断-分析-清除-加固

1. **响应**

   事件类型判断 保持第一现场 信息收集

2. **阻断**

   切断网络、阻断传播、隔离核心资产、隔离受害主体

3. **分析**

   日志分析、流量分析、样本分析、

4. **清除**

   杀进程、删文件、恢复受害主体

5. **加固**

   打补丁、补足策略、升级设备、完善防御流程



### SQL注入

> 联合Union注入
>
> 文件读写
>
> 报错注入：Updataxml、ExtractValue、floor
>
> 盲注： 1. 时间盲注 2. 布尔盲注
>
> 宽字节注入
>
> 堆叠注入
>
> user-agent、Cookie注入
>
> 万能密码、弱口令

### 序列化和反序列化

> **序列化**是将对象转化成字节的过程。
>
> **反序列化**是将字节转化成对象的过程。

#### 序列化的目的

1. **持久化**。对象存储在JVM的栈区，序列化可以将对象转化为字节序列存储在硬盘中实现持久化。
2. **网络传输**。网络传输的数据必须是序列化后的对象。

#### 反序列化漏洞成因

> 在**身份验证**，**文件读写**，**数据传输**等功能处，在未对反序列化接口做访问控制，未对序列化数据做加密和签名，加密密钥使用硬编码（如Shiro 1.2.4），使用不安全的反序列化框架库（如Fastjson 1.2.24）或函数的情况下，由于序列化数据可被用户控制，攻击者可以精心构造恶意的序列化数据（执行特定代码或命令的数据）传递给应用程序，在应用程序反序列化对象时执行攻击者构造的恶意代码，达到攻击者的目的。

### 内网渗透思路

> - 代理穿透
> -  权限维持
> - 内网信息收集
> - 口令爆破
> - 凭据窃取
> - 社工
> - 横行和纵向渗透
> - 拿下域控

### SQL注入getShell

#### 条件

1. 要知道网站绝对路径，可以通过报错，phpinfo界面，404界面等一些方式知道

2. gpc没有开启，开启了单引号被转义了，语句就不能正常执行了

3. 要有file权限，默认情况下只有root有

4. 对目录要有写权限，一般image之类的存放图片的目录就有

#### 传统

> ```sql
> 1'union select 1,'<?xxxxxx;?>'INTO OUTFILE '/var/www/tmp/shell.php'#
> 
> ?id=1 INTO OUTFILE '物理路径' lines terminated by  （一句话木马hex编码）#
> 
> 0x20273c3f70687020406576616c28245f504f53545b2767275d删除293b3f3e27
> ```

`lines terminated by` 可以理解为 以每行终止的位置添加 xx 内容

#### Sqlmap写shell

写入方法和`lines terminated by`相同

> ```sql
> --os-cmd="net user" #命令执行
> 
> --os-shell #生成两个文件，tmpbshrd.php和tmpucnll.php 分别为命令执行和文件上传
> 
> --file-write "./mst/mst.txt" --file-dest "网站的绝对路径/1.php" #写本地shell
> ```

#### Log写入

1. ```mysql
   show variables like '%general%';
   # 查看配置
   ```

2. ```mysql
   set global general_log = on;                   
   # 开启general log模式
   ```

3. ```mysql
   set global general_log_file = 'E:/study/WWW/evil.php';   
   # 设置日志目录为shell地址
   ```

4. ```mysql
   select '<?php xxxxx;?>'
   # 写入shell
   ```

5. ```mysql
   set global general_log=off;                     
   # 关闭general log模式
   ```





## 端口详情

> 21 ftp
> 22 SSH
> 23 Telnet
> 80 web
> 80-89 web
> 161 SNMP
> 389 LDAP
> 443 SSL心脏滴血以及一些web漏洞测试
> 445 SMB
> 512,513,514 Rexec
> 873 Rsync未授权
> 1025,111 NFS
> 1433 MSSQL
> 1521 Oracle:(iSqlPlus Port:5560,7778)
> 2082/2083 cpanel主机管理系统登陆 （国外用较多）
> 2222 DA虚拟主机管理系统登陆 （国外用较多）
> 2601,2604 zebra路由，默认密码zebra
> 3128 squid代理默认端口，如果没设置口令很可能就直接漫游内网了
> 3306 MySQL
> 3312/3311 kangle主机管理系统登陆
> 3389 远程桌面
> 4440 rundeck 参考WooYun: 借用新浪某服务成功漫游新浪内网
> 5432 PostgreSQL
> 5900 vnc
> 5984 CouchDB http://xxx:5984/_utils/
> 6082 varnish 参考WooYun: Varnish HTTP accelerator CLI 未授权访问易导致网站被直接篡改或者作为代理进入内网
> 6379 redis未授权
> 7001,7002 WebLogic默认弱口令，反序列
> 7778 Kloxo主机控制面板登录
> 8000-9090 都是一些常见的web端口，有些运维喜欢把管理后台开在这些非80的端口上
> 8080 tomcat/WDCP主机管理系统，默认弱口令
> 8080,8089,9090 JBOSS
> 8083 Vestacp主机管理系统 （国外用较多）
> 8649 ganglia
> 8888 amh/LuManager 主机管理系统默认端口
> 9200,9300 elasticsearch 参考WooYun: 多玩某服务器ElasticSearch命令执行漏洞
> 10000 Virtualmin/Webmin 服务器虚拟主机管理系统
> 11211 memcache未授权访问
> 27017,27018 Mongodb未授权访问
> 28017 mongodb统计页面
> 50000 SAP命令执行
> 50070,50030 hadoop默认端口未授权访问

## 参考

[1] [渗透面试总结](https://www.cnblogs.com/hetianlab/p/13445349.html)

[2] [护网无敌干货集合 | 护网面试题记录](https://zhuanlan.zhihu.com/p/372064512)

[3] [护网各大厂商面试题汇总 第三版](https://blog.csdn.net/zhaoshiwei188/article/details/115068460)

