---
author: "Yiming Shen"
date: 2021-07-19
lastmod: 2021-07-19
title: "学习Markdown语法"
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

##  *2021年7月18日*

 ### <font color=brown>了解Markdown</font>

Markdown是一种轻量级标记语言，属于HTML格式的一种，易读易写易更改，处处透着极简主义的影子。在GitHub等网站上使用极广，更可以跨平台使用。

### Windows桌面端软件选择

Web网页版有简书等知名博客网站可作记录，千挑万选后我选择了Typora。优点如下：

1. 有中文版。这一点可能不足为道，因为菜单的语言并不影响我们的使用，然而，不支持中文意味着软件对中文输入法的适配甚至中文文本的显示都极为差劲，这一点是我由MarkdownPad的使用中发现的。
2. Typora支持即时渲染技术，非常方便新人学习熟练。
3. Typora可以将md文件导出为pdf格式作发布用，对于笔记爱好者来说成就感满满。

### Markdown语法

> 区块引用	

代码引用：

`printf("使用反引号包裹代码")`

```c++
int main()
{
    int a;
    scanf("%d",&a);
    printf("%d",a++);
    return 0;
}
```

emoji: :smile:

公式：
$$
lim_{x \to\infty} \exp(-x) = 0
$$
插入图片：

<img src="https://pic1.zhimg.com/50/v2-76d7f2bc70de8d0f9af45b75929bf280_r.jpg" alt="插入图片" style="zoom:15%;" />

[^脚注]: 可以用这种方式写脚注。



