---
author: "Yiming Shen"
date: 2022-03-10
lastmod: 2022-03-10
title: "CentOS8.3 服务器配置"
description: "Lnmp、docker、syncthing"
tags: [
    "Tech",
    "Web",
    "Note",
    "Linux",
]
categories: [
    "Notes", 
    "PHP",
    "Technology",
]
math: true
ShowToc: true
draft: false
typora-root-url: ..\..\..\static\
---

## Readme

> 出于学习PHP等后端语言和搭建靶场的目的，我决定购置一台Linux服务器。(PHPStudy：那我走？)

本以为整个流程走下来又是废寝忘食的数天，结果格外顺利，仅用不连续的数小时就完成了系统配置和各个基本应用的安装，成功代理了hugo的静态站点，为了熟悉流程和记住常用命令，我用今天剩下的时间记录过程，以备未来查看。

## 购置云服务器、域名解析、备案

### 服务器选择

阿里云有学生机(开发者计划)，腾讯云有打折的轻量应用服务器，考虑到学生机常有而打折不常有，再加上我的域名也是在腾讯云上买的，便选择了腾讯云的轻量应用服务器(40￥一年)，系统选择了CentOS 8.3，没什么特别的原因，就感觉CentOS服务器都在用，也算是试图跳出常用ubuntu的舒适区一下吧。

### 域名解析

> 关于域名选择可以参考我的另一篇文章:[域名选择：有哪些很酷的域名？](https://shenyiming.life/post/essays/domain_choice/)

域名解析没什么特别之处，两个A记录分别指向www和@，另外在未来想设置多个二级域名用于博客、靶场、邮箱服务，也要通过A记录进行解析。

解析完并配置好lnmp后，网站立刻变成了提示备案……在备案完成前只能通过ip访问。

## 系统配置

### 1. SSH登录

> 有很多主机的root账户使用了诸如123456的弱密码，极其易被暴力破解抓作肉鸡，最好的方法是采用密钥登录。

在腾讯云服务器管理面板内创建密钥（类似Github)，获得一个私钥文件，务必妥善保存，将服务器绑定该密钥后，使用ssh连接软件配置载入私钥文件即可实现无密码登录root用户。

<img src="/serversettings.assets/%7D/image-20220310180935649.png" alt="image-20220310180935649" style="zoom:50%;" />

### 2. 安装并使用Screen



### 3. 安装Lnmp



### 4. 安装配置docker



## 使用Syncthing同步网站



## Nginx配置



## Vscode远程开发管理

