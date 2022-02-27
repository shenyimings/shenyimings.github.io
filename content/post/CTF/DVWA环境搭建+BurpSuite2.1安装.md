---
author: "Yiming Shen"
date: 2021-08-05
lastmod: 2021-09-05
title: "DVWA环境搭建 + BurpSuite2.1安装 + Chrome配置"
tags: [
    "CTF",
    "Note",
]
categories: [
    "Notes",
     "CTF",
]
math: true
---

# 

[TOC]

## DVWA环境搭建

>[DVWA（Damn Vulnerable Web Application）](https://link.zhihu.com/?target=http%3A//www.dvwa.co.uk/)是一个用来进行安全脆弱性鉴定的PHP/MySQL Web应用，旨在为安全专业人员测试自己的专业技能和工具提供合法的环境，帮助web开发者更好的理解web应用安全防范的过程。

DVWA运行基于LAMP（Linux+Apache+MySQL+PHP），出于时间和个人能力的考虑，很难成功地从0开始搭建这样一套体系，故而使用小皮的PHPStudy环境。

PHPStudy：https://www.xp.cn/

DVWA：http://www.dvwa.co.uk/

### 一、安装PHP Study

### 二、配置DVWA

将下载的DVWA压缩包解压到PHP Study的WWW目录下并解压，进入目录中的config文件夹，编辑config.inc.php文件，找到`_DVWA['db_password']='root'`行，将user改为‘root’；password改为‘root’。

### 三、配置PHP Study

开启Apache和PHP服务，点击左边栏*网站*，左上角*创建网站*，域名为设置为本机本地域名，查看本地域名方法：进入`powershell`输入命令`ipconfig`查看，我电脑本机域名为*192.168.0.104*

为什么不设置为localhost *127.0.0.1*：Burp Suite配合chrome浏览器抓包时可能无法拦截localhost的数据。



根目录指向刚刚解压的DVWA-master目录下，点击确认，网站创建成功。

### 四、浏览器进入192.108.0.104 

点击*Create/Reset Database*创建数据库

用户名：admin 默认密码：password

登陆进入后即部署成功。

## BurpSuite2.1安装

>BurpSuite 是用于攻击web 应用程序的集成平台，包含了许多工具。Burp Suite为这些工具设计了许多接口，以加快攻击应用程序的过程。所有工具都共享一个请求，并能处理对应的HTTP 消息、持久性、认证、代理、日志、警报。

### 1. JDK及BurpSuite破解版下载

链接：https://pan.baidu.com/s/11lrFGT0aGCd4BQcAJrL55w
提取码：w5lg

### 2. 配置JAVA环境

安装好jdk后，右键计算机-属性-系统设置-高级-环境变量

新增两个环境变量

1. `%JAVA_HOME%=`jdk安装文件夹所在位置，如`...\jdk1.8.0_201`

2. `%CLASSPATH%=.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar`

修改path变量，分别添加jdk和jre安装文件夹下的bin目录。

### 3. 激活BurpSuite

jdk配置完毕后，先打开burp-loader-keygen.jar文件

点击`run`

将license许可证复制入弹出的框中，点击`Next`，选择`Manual activation`手动激活。

将生成的申请码粘贴入注册机中，自动算号，将生成的激活码复制入框中，点击`Next`即激活成功。

此后想打开Burp Suite，双击运行目录下的VBS脚本即可。



## BurpSuite Chrome配置



### 1. 设置流量代理Proxy

​		在谷歌应用商店中安装`Falcon Proxy`，添加一个新的代理`127.0.0.1:8080`，注意和Burp Suite中代理监听器保持一致。

### 2. 导入SSL证书以便抓取HTTPS网站内容。

​		挂好代理后只能访问http网址，对于https网址则提示不安全，解决方法：访问`http:\\burp\`，点击右上角下载证书certification，得到一个`.der`文件，在chrome浏览器进入设置-隐私设置和安全性-管理证书，点击*受信任的根证书颁发机构*，导入刚刚得到的der证书，注意格式选择所有文件。 

## 参考

[1] [DVWA完整搭建教程](https://blog.csdn.net/qq_45756971/article/details/110001169)

[2] [Burp Suite超详细安装教程](https://blog.csdn.net/LUOBIKUN/article/details/87457545)

[3] [Burp Suite + Chrome详细抓包教程](https://www.jianshu.com/p/eba4077ab1ef)

[4] [Burp Suite不能拦截localhost](https://blog.csdn.net/qq_43422918/article/details/114939896)