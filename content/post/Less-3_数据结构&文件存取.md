---
author: "Yiming Shen"
date: 2022-01-29
lastmod: 2022-01-31
title: "Less-3 数据结构&文件存取"
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

## 数据类型

![MATLAB中的数据类型](https://img-blog.csdnimg.cn/20191112184307618.png)

### 数值类型

默认为`double`类型，可用`int8()`转换为int型

| 数值型             | 描述 |
| ------------------ | ---- |
| `double`           | 双精度浮点数 |
| `single` | 单精度浮点数 |
| `uint8 uint16 uint32 uint64 ` | 8~64位无符号整数 |
| `int8 int16 int32 int64`           | 8~64位带符号整数 |

### 字符型

> 字符型在MATLAB中以字符矩阵形式存储，可使用矩阵索引对其进行操作。

```matlab
str3 = [str1 str2];
size(str3)

str4 = 'aardvark'
'a' == str4 % 判断每个索引位置是否为a并返回结果 [1 1 0 0 0 1 0 0]
str4(str4 == 'a') = 'A' % 将str4中为a的位置替换为A 'AArdvArk'
```



### 结构体(Structure)

>  MATLAB中的结构体类似Python中字典，键值一一对应。

```matlab
student.name = 'John Doe';
student.id = 'jdo2@sfu.ca';
student.number = 301073268;
student.grade = [100, 75, 73; ...
                 95, 91, 85.5; ...
                 100, 98, 72];
student

```



### 元胞数组(Cell)

> MATLAB中，元胞数组可以同时容纳不同类型元素的多维数据结构，类似于Python中的列表。

使用`{}`定义元胞数组

![2019111414434878](https://img-blog.csdnimg.cn/2019111414434878.png)

```matlab
A = {[1 4 3; 0 5 8;7 2 9]  'Anne Smith'; 3+7i -pi:pi:pi}
A

%%单元索引
A(1,1)={[1 4 3; 0 5 8; 7 2 9]};
A(1,2)={'Anne Smith'};
A(2,1)={3+7i};
A(2,2)={-pi:pi:pi};
A
%%

%% 内容索引
A{1,1}=[1 4 3; 0 5 8; 7 2 9];
A{1,2}='Anne Smith';
A{2,1}=3+7i;
A{2,2}=-pi:pi:pi;
A
%%
```

> - 使用**单元索引**`()`，得到一个**子元胞数组**
> - 使用**内容索引**`{}`，得到**元胞数组对应区域的内容**

##### 使用`cat`函数可在指定维度对cell进行拼接

```matlab
A = [1 2;3 4];
B = [5 6;7 8];

C = cat(1,A,B) % 按行拼接
% 1 2
% 3 4
% 5 6
% 7 8
C = cat(2,A,B) % 按列拼接
% 1 2 5 6
% 3 4 7 8


```



## 文件读写

### MATLAB支持的文件类型

| 扩展名         | 文件                      | 读取函数                 | 写入函数                           |
| -------------- | ------------------------- | ------------------------ | ---------------------------------- |
| `*.mat`        | MATLAB二进制源文件        | `load(filename)`         | `save(filename,variables)`         |
| `*.xls *.xlsx` | EXCEL表格                 | `xlsread`                | `xlswrite`                         |
| `*.txt`        | 空格分割的数字（类似csv） | `load(filename,"-ascii)` | `save(filename,variables,"-ascii)` |

> 复杂的数据格式，structure和cell不支持以`*.mat`格式存储 就很迷

### 读写EXCEL表格

使用`xlsread`和`xlswrite`函数读写EXCEL数据

`[num,txt,raw] = xlsread(filename,sheet,xlRange)`

![2019111416031957](https://img-blog.csdnimg.cn/2019111416031957.png)

```matlab
Score = xlsread('04Score.xlsx')
Score = xlsread('04Score.xlsx', 'B2:D4')
[Score Header] = xlsread('04Score.xlsx')


M = mean(Score)';
xlswrite('04Score.xlsx', M, 1, 'E2:E4');
xlswrite('04Score.xlsx', {'Mean'}, 1, 'E1');

```



## 参考

[1] [MATLAB03:数据类型与文件读写](https://blog.csdn.net/ncepu_Chen/article/details/103065580)
