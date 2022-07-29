---
author: "Yiming Shen"
date: 2021-07-23
lastmod: 2021-07-23
title: "MISC类型题目及技巧"
tags: [
    "CTF",
    "MISC",
    "Note",
]
categories: [
    "Notes",
     "CTF",
]
math: true
typora-root-url: ..\..\..\..\static\
---

### 图片隐写

1. 图片EXIF信息
2. GIF图片
3. 文件头 16进制编码

### 编码

[Readflag](https://readflag.cn/)

### 社工

1. [Bugku社工_进阶收集](https://ctf.bugku.com/challenges/detail/id/188.html)

   根据题目给出的说说截图，有如下信息：

   * 终点位置突出特征：有塔
* 坐地铁需要7站，中间还要转一站
   * 出发站为始发站
   * 家位置在始发站和第二站的中间位置
   
   根据以上信息，先对说说配图进行百度识图，查找到地理位置为西安大雁塔，对照西安地铁图，距离大雁塔站有7站且为始发站的站点只有韦曲南站，至此即可确定小美所住的小区在韦曲南站和航天城站之间，利用百度地图标尺画两个圆，可以大致确定符合条件的7家小区，挨个尝试可得flag。

2. [Bugku_隐写](https://ctf.bugku.com/challenges/detail/id/3.html))

   题目文件为一张PNG格式图片，Windows自带图片浏览器无法正常打开该图片，说明图片内容经过人为篡改。使用工具TweakPng查看png内容，将图片大小的chunk部分高度修改为宽度一致，500px，保存，图片flag得以正常显示。

   正常做法：用Python脚本计算和图片大小相匹配的正常宽高，再利用Tweakpng查看并修改。

