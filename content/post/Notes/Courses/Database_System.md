---
author: "Yiming Shen"
date: 2022-03-15
lastmod: 2022-03-15
title: "数据库系统系统导论笔记"
tags: [
   "SQL",
    "Note",
]
categories: [
    "Notes", 
"courses",
]
math: true
ShowToc: true
draft: false
typora-root-url: ..\..\..\static\
---

## 数据库系统导论

讯飞输入法对实体键盘的适配好得多。

### 2 关系运算符

$$
\sigma \quad 选择\\\\
\pi 投影\\\\
连接\\\\
除
$$

连接和除是复合运算

#### 记号

$$
设关系模式为R(A_1,A_2,\cdots,An)\\\\
A_i A_i \cdots 为属性列\\\\
R(X,Z) \\\\
t[X]=x \quad Z_x = \{t[Z]|t\in R ,t[X]=Z\}
$$



#### 选择

在关系R 中选择满足给定条件的诸元组

F为选择条件 取值真或假

查询IS系全体学生：
$$
\sigma_{Sdept='IS'}(Student)
$$

#### 投影

从关系R中选择出若干属性列组成新的关系
$$
\pi_A(R)=\{t[A]|t\in R\}
$$
A为R中的属性列

> 投影操作主要是从列的角度进行运算

查询学生的姓名和所在系:
$$
\Pi _{Sname,Sdept}(Student)
$$
