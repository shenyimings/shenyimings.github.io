---
author: "Yiming Shen"
date: 2022-01-15
lastmod: 2022-01-20
title: "Less-1 MATLAB基本操作与矩阵输入"
tags: [
    "MATLAB",
    "数学建模",
    "Note",
]
categories: [
    "Notes", 
    "Maths",
]
math: true
ShowToc: true
draft: false
typora-root-url: ..\..\static\
---

## 基本操作

*命令行模式*

### 公式输入

直接输入算式即可计算结果

输入格式基本同自然语言

计算：
$$
cos(\sqrt{(\frac{1+2+3+4}{5})})
$$


```MATLAB
cos(sqrt((1+2+3+4)/5))
```

注意：5/2 是5除以2 5\2是2除以5

### 变量定义

定义变量与python相同，无需声明，默认类型为double

不能声明为变量的系统关键字：![image-20220115225121626](/Less-1-MATLAB基础.assets/image-20220115225121626.png)

显示格式

```matlab
format long
```

格式种类：

![image-20220115225042103](/Less-1-MATLAB基础.assets/image-20220115225042103.png)

```matlab
clc #清屏
clear a #删除变量a
who #查询变量
whos #查询变量详细信息
```







## 矩阵输入

### 矩阵定义

```matlab
a = [1 2 3 4] #定义1*4矩阵
b = [1;2;3;4] #定义4*1矩阵
a = [1 2 3 4;1 2 3 4;1 2 3 4;1 2 3 4] #定义4*4矩阵

```

### 矩阵索引

#### 取/修改某个值

```matlab
a(8) #取第8个值（按行从上往下数）a(8)=4
a([1 3 5])#取第1 3 5个值
a([1 3;1 3])#取第1个数和第3个数 两遍
a(3,2) #取第三行第二列的数
a([1 2],[2 3])#取第1行第2行和第2列第3列的交集为一个新矩阵

k = [k,10]
k(end+1) = 10;
k = [k(1:5),5.5,k(6:end)]
```

修改：a()=[]

#### 迭代生成

```matlab
a = 1:100
b = 1:2:99 #步长为2 1-99
str = 'a':2:'z'

a(1,:) #输出第一行的值
a(:,1) #输出第一列的值
```

### 矩阵连接（增广）

``` matlab
F = [A B] #横向合成
F = [A;B] #纵向合成

```

### 矩阵运算（线代）

```matlab
y1 = A * B #矩阵乘法
y2 = A .* B #矩阵点乘，(1,1)*(1,1) (1,2)*(1,2)
B' #B矩阵的转置
eye(n) #生成一个n阶单位矩阵
zeros(n,m) #生成一个n*m的零矩阵
ones(n,m) #生成一个n*m的1矩阵（全是1）
diag([2 3 4]) #单位对角化
linspace(0,13,6) #按比例生成从0-13的6个数


```

![image-20220115225044216](/Less-1-MATLAB基础.assets/image-20220118103144216.png)

![image-20220118105503687](/Less-1-MATLAB基础.assets/image-20220118105503687.png)


```matlab
mean(A) #求A的列平均数
mean(mean(A)) #A的全部平均数
find(A==1) #找到矩阵A中值为1的绝对位置
```

