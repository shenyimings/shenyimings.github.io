---
author: "Yiming Shen"
date: 2022-02-26
lastmod: 2022-03-03
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

## Block & Inline

下面是HTML中的块级元素

![image-20220301172756982](/HTML_Tutorial.assets/%7D/image-20220301172756982.png)

下面是内联元素

![image-20220301172837306](/HTML_Tutorial.assets/%7D/image-20220301172837306.png)

> **注意：内联元素不能包含块级元素。**

### 关于`<span>`的用法

```html
<!DOCTYPE html>
<html>
<body>

<h1>The span element</h1>

<p>My mother has <span style="color:blue;font-weight:bold">blue</span> eyes and my father has <span style="color:darkolivegreen;font-weight:bold">dark green</span> eyes.</p>

</body>
</html>
```

效果：


<h1>The span element</h1>

<p>My mother has <span style="color:blue;font-weight:bold">blue</span> eyes and my father has <span style="color:darkolivegreen;font-weight:bold">dark green</span> eyes.</p>

### 关于`<div>`的用法

```html
<div style="background-color:black;color:white;padding:20px;">
  <h2 style="color:white">London</h2>
  <p>London is the capital city of England. It is the most populous city in the United Kingdom, with a metropolitan area of over 13 million inhabitants.</p>
</div>
```

效果：

<div style="background-color:black;color:white;padding:20px;">  <h2 style="color:white">London</h2>  <p>London is the capital city of England. It is the most populous city in the United Kingdom, with a metropolitan area of over 13 million inhabitants.</p></div>

## `class`属性

> `class`属性通常用于指向css样式表中的类名

```html
<!DOCTYPE html><html><head><style>.city {  background-color: tomato;  color: white;  border: 2px solid black;  margin: 20px;  padding: 20px;}</style></head><body><div class="city">  <h2>London</h2>  <p>London is the capital of England.</p></div><div class="city">  <h2>Paris</h2>  <p>Paris is the capital of France.</p></div><div class="city">  <h2>Tokyo</h2>  <p>Tokyo is the capital of Japan.</p></div></body></html>
```

(markdown似乎无法显示定义在外部的css样式)

使用不同的class类名从而赋予分块不同的css样式

>  **`class`类名区分大小写。**

一个html元素可以属于多个类，**用空格分割**

```html
<h2 class="city main">London</h2>
<h2 class="city">Paris</h2>
<h2 class="city">Tokyo</h2>
```

## `id`属性

> `id`属性用于给HTML元素指定一个唯一的标识。

#### 应用独立的CSS样式

```css
#myHeader {
  background-color: lightblue;
  color: black;
  padding: 40px;
  text-align: center;
}
```

*给id为myheader的元素应用样式*

**注意：id名区分大小写，同一个id在一页只能出现一次。**

#### 书签跳转

```html
<a href="#C1">Jump to Chapter 1 </a>

<h2 id="C1">Chapter 1</h2>
```

也可指向其他文件中的id：

```html
<a href="html_demo.html#C1">Jump to Chapter 1</a>
```

### 通过JavaScript调用

<h1 id="myHeader">Hello World!</h1>
<button onclick="displayResult()">Change text</button>

<script>
function displayResult() {
  document.getElementById("myHeader").innerHTML = "Have a nice day!";
}
</script>

```html
<h1 id="myHeader">Hello World!</h1>
<button onclick="displayResult()">Change text</button>

<script>
function displayResult() {
  document.getElementById("myHeader").innerHTML = "Have a nice day!";
}
</script>
```

## `iframe`标签

> 内联iframe标签可以将另一个html文档的内容嵌入当前文档

代码示意

```html
<iframe src="demo_iframe.htm" style="border:none;" title="Iframe Example"></iframe>
```

## HTML实体

| Result | Description                        | Entity Name | Entity Number |
| :----- | :--------------------------------- | :---------- | :------------ |
|        | non-breaking space                 | &nbsp;      | &#160;        |
| <      | less than                          | &lt;        | &#60;         |
| >      | greater than                       | &gt;        | &#62;         |
| &      | ampersand                          | &amp;       | &#38;         |
| "      | double quotation mark              | &quot;      | &#34;         |
| '      | single quotation mark (apostrophe) | &apos;      | &#39;         |
| ¢      | cent                               | &cent;      | &#162;        |
| £      | pound                              | &pound;     | &#163;        |
| ¥      | yen                                | &yen;       | &#165;        |

