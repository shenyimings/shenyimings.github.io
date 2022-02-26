---
author: "Yiming Shen"
date: 2022-02-26
lastmod: 2022-02-26
title: "HTML Tutorial"
tags: [
    "HTML",
    "Web",
    "Note",
]
categories: [
    "Notes", 
    "PHP",
]
math: true
ShowToc: true
draft: false
typora-root-url: ..\..\..\static\
---

## Code

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Hello World</title>
		<style>
		body {background-color: powderblue;}
		h1   {color: blue;}
		p    {color: red;}
		</style>
	</head>
	<body>
		<h1 style="text-align: center;">一级标题</h1>
		<p style="color:red">段落</p>
	  	<hr>
		<h1 style="background-color:rgb(126, 161, 196);color:white;text-align: center;font-size: 150%;padding: 10px;font-family: Times New Roman; ">rgb(255, 148, 41)</h1>
		<p><em>测试</em></p>
		<p><i>测试2</i></p>
		<p><mark>测试3</mark></p>
		<img src="https://shenyiming.life/apple-touch-icon.png" title="Shenyiming" alt="ShenYiming" width = "128" height = "128">
		<a href="https://shenyiming.life"><b>ShenYiming.Life</b></a>

		<p>Browsers usually indent blockquote elements.</p>

		<blockquote cite="http://www.worldwildlife.org/who/index.html">
For nearly 60 years, WWF has been protecting the future of nature. The world's leading conservation organization, WWF works in 100 countries and is supported by more than one million members in the United States and close to five million globally.
</blockquote>
		<p><q>c++</q>
        <button onclick="document.location='https://shenyiming.life'">Sheny</button>
        <img src="workplace.jpg" alt="Workplace" usemap="#workmap">

<map name="workmap">
  <area shape="rect" coords="34,44,270,350" alt="Computer" href="computer.htm">
  <area shape="rect" coords="290,172,333,250" alt="Phone" href="phone.htm">
  <area shape="circle" coords="337,300,44" alt="Coffee" href="coffee.htm">
</map>
	</body>
</html>
<!--A comment-->
```

