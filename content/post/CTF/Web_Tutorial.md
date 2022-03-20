---
author: "Yiming Shen"
date: 2021-11-04
lastmod: 2022-03-07
title: "Web方向入门课程-大纲"
tags: [
    "CTF",
    "Note",
]
categories: [
    "Notes",
     "CTF",
]
math: true
showtoc: true
tocopen: true
typora-root-url: ..\..\..\static\
cover:
    image: "/Web_Tutorial.assets/%7D/image-20220307201418125.png"
---

> 主讲人：HnuSec - 申一鸣

## 1. CTF与网络安全

![image-20220307201536151](/Web_Tutorial.assets/%7D/image-20220307201536151.png)

## 2. CTF-Web方向简介

![image-20220307201559667](/Web_Tutorial.assets/%7D/image-20220307201559667.png)

## 3. 以Bugku例题(*sql注入*)引出CTF中的Web题型

![image-20220307201625576](/Web_Tutorial.assets/%7D/image-20220307201625576.png)

题目- [sql注入](https://ctf.bugku.com/challenges/detail/id/112.html)

博客-[SQLI-labs作者博客](http://dummy2dummies.blogspot.com/2012/06/sqli-lab-series-part2.html)

使用C语言举个引入所谓SQL注入漏洞的例子（实际并不存在）

``` c
scanf("%s",&a);


//倘若用户输入:  ");return 0;//
scanf("");return 0;//);
```

而在mySQL语言中：

```sql
SELECT * FROM users WHERE id='1' LIMIT 0,1
# 是注释符号
# 倘若用户输入:  '1 or 1=1 #
SELECT * FROM users WHERE id='1' or 1=1 # LIMIT 0,1
```

### SQL盲注

[如何用一台真理机器得到可控核聚变的文档？](如果有一台真理机器只能回答是或不是，能对人类社会作多大贡献？ - 龙牙的回答 - 知乎 https://www.zhihu.com/question/324557531/answer/688214123)



```mysql
1'or((ascii(mid((select(password)from(admin))from(/*第几位*/))))<>/*字符ASCII码*/)or(1)#
```

`4dcc88f8f1bc05e7c2ad1a60288481a2`



## 4. Web方向技能树

|         经济基础          | 上层建筑         |
| :-----------------------: | ---------------- |
| Linux(Kali使用、LAMP搭建) | 脚本小子         |
|          Python           | 工具自给自足     |
|    HTML+CSS+Javascript    | 代码审计         |
|        PHP/Go/Java        | 后端安全         |
|  Vue/React/Flask/Django   | 框架安全         |
|       MySQL/SQLlite       | 数据库安全       |
|           C/C++           | 深层逻辑漏洞挖掘 |
|     HTTP、TCP/IP协议      | 深层网络安全     |
|   数论、群论、近世代数    | 通信安全         |



## 5. 入门方向：夯实基础 or 做题提升？

**建议先做题，在做题的同时学习涉及的相关知识补足短板**

bugku、MoeCTF西电新生赛等招新类比赛（入门了解）

自建靶场：DVWA、SQLI-labs、Upload-labs（了解各种题目类型的基本知识）

ctfhub（题目实战）

ctfshow

各种比赛真题

## 6. 常用工具及作业讲解

### 工具

[CTFhub工具下载](https://www.ctfhub.com/#/tools)

Kali Linux

PHPStudy（自建靶场）

Burpsuite

Hackbar（浏览器插件）

SQLmap（SQLI）

tplmap（SSTI）

Stegsolve（MISC）

AntSword中华蚁剑/中华菜刀（WebShell）

Nmap、Nessus、Metasploit（漏洞扫描）

### 预习作业 

11月6日web入门课前预习作业（先完成Air师傅的课后笔记）

1. （选做）卸载电脑上的流氓杀毒软件（原因：效果不如Windows自带的defender，影响渗透工具使用。考察：操作系统基本使用）

2. 尝试永久一次性关闭Windows defender（原因：效果太好。。严重影响渗透工具使用和学习，会造成软件莫名其妙错误和记录危险代码的笔记丢失。考察：信息搜集+操作系统深度使用）
**杀毒软件可用火绒代替，裸奔需谨慎**。

3. 了解SQL数据库与SQL注入相关知识（考察：信息搜集+自学能力）

4. 安装重要渗透工具Burpsuite（考察：有效信息筛选）

5. 安装浏览器插件Hackbar（考察：科学上网）

其中第三条涉及到下次讲课内容，请同学们尽量学习了解。以上作业量力而行，不会做、做不完可以不用记在笔记上，关键在于学习的态度和自觉性，持之以恒的学习最为宝贵。笔记记完后请以PDF形式发至邮箱hnusec@163.com作为实验室招新重要参考。

### 课后作业

七道2019年海南省省赛web题，题解附上。

xss
http://192.144.182.32:8001/abf20c91a442da48/7/level.php

Dream II
http://192.144.182.32:8001/abf20c91a442da48/4/

头等舱
http://192.144.182.32:8001/abf20c91a442da48/12/

JS 
http://192.144.182.32:8001/abf20c91a442da48/5/game.html

代码审计
http://192.144.182.32:8001/abf20c91a442da48/13/

include
http://192.144.182.32:8001/abf20c91a442da48/2/

上传
http://192.144.182.32:8001/abf20c91a442da48/6/index.php
