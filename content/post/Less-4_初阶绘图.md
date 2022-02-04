---
author: "Yiming Shen"
date: 2022-02-03
lastmod: 2022-02-04
title: "Less-4 MATLAB初阶绘图"
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
## 图线绘制

> 在MATLAB中，使用`plot()`函数绘制图线，语法为：

```matlab
plot(x,y,linespec)
```

- `x`:图线的横坐标
- `y`:图线的纵坐标

可以仅指定x或者y变量内容，另一参数由系统自动按由小到大顺序生成整数数列。

#### 绘制(0,2π)内余弦函数图像

```matlab
x = 0:pi/20:2*pi;
y = cos(x);
plot(x,y)
```



## 图线装饰

### 线条参数
- `linespec`图线的线条参数

  > 由**线型**，**标记符号**，**颜色**这三个**设定符**组成的字符串，**设定符**不区分先后顺序。

| 线性         | 设定符 | 标记 | 设定符 | 颜色 | 设定符 |
| ------------ | ------ | ---- | ------ | ---- | ------ |
| 实线（默认） | `-`    | 圆圈 | `o`    | 红色 | `r`    |
| 虚线         | `--`   | 加号 | `+`    | 绿色 | `g`    |
| 点线         | `:`    | 星号 | `*`    | 蓝色 | `b`    |
| 点划线       | `-.`   | 叉号 | `x`    | 黑色 | `k`    |

详情参考MATLAB官方文档 [LineSpec（线条设定）-MathWorks](https://ww2.mathworks.cn/help/matlab/ref/linespec.html)






## 参考

- [1] [MATLAB04:基础绘图](https://blog.csdn.net/ncepu_Chen/article/details/103097452)
- [2] [LineSpec（线条设定）-MathWorks](https://ww2.mathworks.cn/help/matlab/ref/linespec.html)

