---
author: "Yiming Shen"
date: 2022-01-23
lastmod: 2022-01-23
title: "Less-2 结构程序与自定函数"
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

## 脚本模式

脚本模式用于编写结构化程序

示例

```matlab
for i=1:10
    x=linspace(0,10,100);
    plot(x,sin(x+i));
    print(gcf,'-deps',strcat('plot',num2str(i),'.ps'));
end
```

*画0-10内sin图像*

>  - F5 运行程序
>
> - 打断点可调试运行
>
> - % 是注释
>
> - 选中 Ctrl + R 可批量注释
>
> - 选中 Ctrl + I 智能缩进

## 自定函数

> 在某个函数文件中定义函数，**在命令行窗口或脚本模式进行调用。**

### 单变量计算

$$
x = x_0 +v_0t +\frac{1}{2}gt^2
$$

``` matlab
function x =freebody(x0,v0,t)
x = x0 + v0 .*t + 1/2*9.8*t.*t

% 采用点乘 可以以矩阵形式同时计算多组变量

```

### 多变量计算

$$
a = \frac{v_2-v_1}{t_2-t_1} \newline
F=ma
$$

```matlab
function [a F] = acc(v2,v1,t2,t1,m)
a=(v2-v1)./(t2-t1);
F = m.*a;
```

`[Acc Force] = acc(20,10,5,4,1)`

```matlab
inputname % 函数输入变量名

mfilename % 正在运行的函数所在文件名

nargin % 函数输入变量个数

nargout % 函数输出变量个数

varargin % 输入变量的长度

varargout % 输出变量的长度
```

### 函数句柄

> 借助函数句柄，可以方便地画出各类函数的图像，这类绘图函数往往以ez开头，下面我将演示ezplot, ezsurf两个函数。

```matlab
% ezplot画sin函数在[0, 2 * pi]内的曲线

ezplot(@sin, [0, 2 * pi]);
```

## Exercise

1. 编写程序找到矩阵A中的负数，将其坐标存至矩阵B中，并将负数更改为0
   $$
   \left[
    \begin{matrix}
      0 & -1 & 4 \\\\
      9 & -14 & 25 \\\\
      -34 & 49 & 64
     \end{matrix}
     \right]
   $$
   
   ```matlab
   clc;
   A = [0 -1 4;9 -14 25;-34 49 64];
   
   disp(A);
   B = [0];
   for n=1:9
       if A(n)<0
           disp(A(n));
           B(end)=n;
           B(end+1)=0;
           A(n)=0;
       end
   end
   disp(A);
   disp(B);
   ```
   
2. 编写一个将华氏度转换为摄氏度的函数

   > input, isempty, break, disp, num2str
   
   ```matlab
   function  F2C()
   %F2C 将华氏度转换为摄氏度
   %   C: Celsus F:FahrenHeit
   while 1
       F = input('Please input °F >');
       if (isempty(F))
           return;
       end
       C = 5/9 * (F - 32);
       disp(['Temperature in Celsus: ' num2str(C) '℃']);
   end
   end
   ```
   

## 注意事项



在每个脚本的开头应加入以下语句：

```matlab
clear all	% 清空工作区内存中的变量
close all 	% 关闭之前程序绘制的图像
clc			% 清空之前程序在终端的输出
```

