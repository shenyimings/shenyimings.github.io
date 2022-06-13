---
author: "Yiming Shen"
date: 2022-06-11
lastmod: 2022-06-11
title: "PHP 反序列化"
tags: [
    "CTF",
    "PHP",
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

## 基础知识

常见PHP魔术函数

``` php
__construct() //当一个对象创建时被调用

__destruct() //当一个对象销毁时被调用

__wakeup() //使用unserialize时触发

__sleep() //使用serialize时触发

__destruct() //对象被销毁时触发

__call() //在对象上下文中调用不可访问的方法时触发

__get() //用于从不可访问的属性读取数据

__set() //用于将数据写入不可访问的属性

__toString() //把类当作字符串使用时触发

__invoke() //当脚本尝试将对象调用为函数时触发
```



## 参考

1. [PHP反序列化和POP链构造 - As1def's Blog](https://as1def.github.io/2020/10/09/PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E5%92%8CPOP%E9%93%BE%E6%9E%84%E9%80%A0/)
2. [一题教你学会构造PHP反序列化POP链](https://www.freebuf.com/articles/web/247930.html)

