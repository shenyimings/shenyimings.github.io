---
author: "Yiming Shen"
date: 2022-03-15
lastmod: 2022-03-18
title: "数据库系统导论笔记"
tags: [
   "SQL",
    "Note",
]
categories: [
    "Notes", 
"Courses",
]
math: true
ShowToc: true
draft: false
typora-root-url: ..\..\static\
---

## 2 关系运算符 

$$
\sigma \quad 选择\\\\
\pi 投影\\\\
\bowtie 连接\\\\
\div 除
$$

连接和除是复合运算

### 记号

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
可以去重(如查询学生关系中全部系名)，**去掉了标识元组的主码**。

#### 连接

从两个关系的笛卡尔积中选取属性间满足一定条件的元组 **(有条件的笛卡尔积运算)** 
$$
R \bowtie S = \{\}
$$

##### 等值连接

##### 自然连接

> 两个关系比较的**分量是相同的属性组**，结果中把重复的属性列去掉

把相同的连一块

##### 区别

等值连接可比即可 自然连接必须相同

等值连接m+n列 自然连接u-b列

##### 悬浮元组

> 两个关系R和S在做自然连接时，关系R中某些元组有可能在S中**不存在公共属性上值相等的元组**，从而造成R中这些元组在操作时被舍弃了，这些被舍弃的元组被称为悬浮元组。

##### 外连接

> 如果把悬浮元组也保存在结果关系中，而在其他属性上填空值Null，就叫做外连接

左外连接

只保留左边关系R中的悬浮元组

#### 除运算

> 给定关系R（X，Y） S（Y，Z）其中X，Y，Z为属性组
>
> R中的Y和S中的Y可以有不同的属性名，但必须出自相同的域集



条件 元组在X上分量值x的象集Yx包含S在Y上投影的集合

记作
$$
R \div S
=\{t_r [X] | t_r \in R \wedge  \Pi_Y (S)\subseteq Y_x\}
$$


##### 查询至少选修1号课和3号课的学生号码



##### 基本运算导出

$$
R(X,Y)\quad S(Y,Z)\\\\
P(X,Y)=\Pi_X(R)\times \Pi_Y(S)\\\\
\Pi_x(R)-\Pi_x(P(X,Y)-R(X,Y))
$$



## SQL 概述



### SQL的产生和发展

> 结构化查询语句，任何商业化的数据库都支持SQL语句。

### SQL的特点

> 1. **综合统一**
> 2. 高度的非过程化
> 3. 集合操作
> 4. 一种语言，多种实现方式（嵌入式语言）
> 5. 语法简单

<h3 style="color:green">SQL的基本概念</h3>

#### SQL 支持三级模式

> 外模式、模式、内模式

视图－>基本表－>存储文件

#### 基本表

- **独立存在的表**
- SQL中一个关系对应一个基本表
- 一个或多个基本表对应一个存储文件
- 一个表可以带若干索引

#### 视图

- 从一个或几个基本表导出的表
- 数据库**只存放视图的定义**而不放对应数据
- 是**虚表**，只有结构
- 用户可以在视图上再定义视图

#### 存储文件

- 逻辑结构组成内模式
- 对用户不可见

## 学生－课程数据库

学生-课程模式 S-T

## 数据定义

### 模式的定义与删除

##### 模式

> - 一个DBMS可以建立多个数据库
> - 一个数据库中可以建立多个模式
> - 一个模式下通常可以包括多个表、视图、索引等数据库对象

定义模式

```sql
CREATE SCHEMA <NAME> AUTHORIZATION <USERNAME> [<表定义子句> <视图定义子句> <授权定义子句>];
```

- 定义模式实际定义了命名空间

##### 例题

```sql
CREATE SCHEMA "S-T" AUTHORIZATION WANG; 
```

*省略模式名：默认模式名为用户名*

**删除模式**

```sql
DROP SCHEMA <schemaname> <CASCADE | RESTRICT>;
```

`RESTRICT`: 限制非空模式的删除

`CASCADE`: 级联，删除模式同时把该模式的所有数据库对象删除

```sql
DROP SCHEMA zhang CASCADE
```

### 基本表的定义、删除与修改

```sql
CREATE TABLE <tablename> (<colname> <datatype> [<列级完整性约束条件> <列名> <数据类型> [<列级完整性约束条件>...] [<表级完整性约束条件>]])
```

#### 例

```sql
CREATE TABLE Student(
	Sno CHAR(9) PRIMARY KEY,
    Sname CHAR(20) UNIQUE, # 用户自定义完整性
    Ssex CHAR(2),
    Sage SMALLINT, 
    Sdept CHAR(20)

)
```



``` sql
CREATE TABLE Course(
	Cno		Char(4)	PRIMARY KEY,
    Cname 	CHAR(40),
    Cpno	CHAR(4),
    Ccredit	SMALLINT,
    FOREIGN KEY (Cpno) REFERENCES Course (Cno)
    
)
```



```sql
CREATE TABLE SC
(
	...
    PRIMARY KEY (Sno, Cno),
    FOREIGN KEY (Sno) REFERENCES Student(Sno)
)
```

#### 数据类型

- SQL中的域的概念是由数据类型实现的
- 确定运算性质
- 限定取值范围

#### 模式与表

>  一个基本表属于某个模式，一个模式可以包含多个基本表。 

**定义基本表所属模式**

1. 在表名中明显的给出模式名
2. 在创建模式语句中同时创建
3. **设置所属模式**（常用方法）



显示当前搜索路径

```sql
SHOW search_path
#default: $user,Public
```

#### 修改基本表

```sql
ALTER  TABLE <tablename> [ADD [COLUMN] <新列名> <数据类型> [完整性约束]] [ADD <表级完整性约束>] 
[DROP [COLUMN] <列名> [CASCADE | RESTRICT]]
[DROP CONSTRAINT <完整性约束名> [CASCADE | RESTRICT]]
[ALTER COLUMN <列名> <数据类型>]
```

##### 向学生表加入入学时间列

```sql
ALTER TABLE Student ADD S_entrance DATE;
```

##### 将年龄数据类型改为整型

```sql
ALTER TABLE Student ALTER smallint;
```

### 索引的建立与删除

> - 建立索引(数据库管理员、表的属主)
> - 维护索引（关系数据库管理系统）
> - 使用索引（关系数据库管理系统自动选择合适的索引）

#### 建立索引

```sql
CREATE [UNIQUE] INDEX <索引名> ON <表名>(<列名>[<次序>][,<列名>[<次序>]]…)
```

##### 例

按学号升序排列建唯一索引

```sql
CREATE UNIQUE INDEX Stusno ON Student(Sno ASC);
```

`ASC`升序（默认）`DESC`降序

#### 修改索引

```sql
ALTER INDEX <旧索引名> RENAME TO <新索引名>
```

> 没必要修改索引内容，重建即可。

#### 删除索引

```sql
DROP INDEX <索引名>
```

> 系统从**数据字典**中删去有关该索引的描述。

### 数据字典

> 关系数据库管理系统内部的一组系统表，记录了数据库中所有的定义信息：
>
> - 关系模式
> - 视图
> - 索引
> - 完整性约束
> - 用户对数据库的操作权限
> - 统计信息



## 查询



### 带有比较运算符的子查询

> 找出每个学生超过自己选修课程平均成绩的课程号

```sql
SELECT Sno,Cno
FROM SC x
WHERE Grade >= (SELECT AVG(Grade)
               FROM SC y
               WHERE y.Sno=x.Sno)
```

*相关子查询*

### ANY或ALL谓词子查询

> 必须同时使用比较运算

- \> ANY 大于子查询结果中的某个值
- \< ALL 小于查询结果中的所有值

> 查询非计算机系比计算机系任意一个学生年龄小的学生姓名和年龄

```sql
SELECT Sname,Sage
FROM Student
WHERE Sdept<>'CS' AND
	  Sage<ANY(SELECT Sage
              	FROM Student
              	WHERE Sdept='CS');
```

执行内嵌子查询不依赖父查询 👉 不相关子查询



#### 与聚集函数的等价转换关系

|      | =    | <>或!= | <    | <=    | >    | >=    |
| ---- | ---- | ------ | ---- | ----- | ---- | ----- |
| ANY  | IN   | --     | <MAX | <=MAX | >MIN | >=MIN |
| ALL  | --   | NOT IN | <MIN | <=MIN | >MAX | >=MAX |

### 带有EXISTS谓词的子查询

> **存在量词**
>
> - 带有`EXISTS`的子查询不返回任何数据，只判断T或F，内层查询非空为真
>
> - 由`EXISTS`引出的子查询，其目标列表达式通常都用*

查询了所有选修1号课程的学生姓名

```sql
SELECT Sname
FROM Student
WHERE EXISTS(SELECT *
             FROM SC
             WHERE Sno=Student.Sno AND Cno='1');
```

**所有IN、ANY、ALL、比较运算都能用EXISTS等价替换**

SQL 语言没有全称量词$$\forall $$

### 集合查询

`UNION` `INTERSECT` `EXCEPT		`

并	差	交

### 基于派生表的查询

> 子查询可以出现在FROM子句中，生成**临时派生表**作主查询的查询对象。



```sql
SELECT Sno, Cno
FROM SC, (SELECT Sno,Avg(Grade)
         FROM SC
          GROUP BY Sno
         ) AS Avg_sc(avg_sno,avg_grade)
WHERE SC.Sno = Avg_sc.avg_sno AND
	  SC.Grade >= AVG_sc.avg_grade;
```

**`FROM`中的嵌套查询子句需要`AS`定义派生表名**

无聚集函数可以不指明所有的列及列名

### `SELECT`语句的一般形式

```sql
SELECT [ALL|DISTINCT]
FROM <tablename>
WHERE <conditions>
GROUP BY <columnname> [HAVING <conditions>]
ORDER BY <columname> [ASC|DESC];
```

子查询不用`ORDER BY`

## 数据更新

### 插入数据

- 插入元组
- 插入子查询结果

**格式**

`INSERT` `INTO` `VALUES`

**例** 插入新元组至Student表中

```sql
INSERT
INTO SC(Sno,Cno)
VALUES ('201215128','1');
```

如果是完整的插入，无需指明哪几列

### 插入子查询的结果

**例** 求各系学生平均年龄，存入数据库

```sql
CREATE TABLE Dept_age
(
    Sdept	CHAR(15)
    Avg_age	SMALLINT
);

INSERT
INTO	Dept_age(Sdept,Avg_age)
SELECT	Sdept,AVG(Sage)
FROM	Student
GROUP	BY	Sdept;
```

### 插入数据的完整性约束

> DBMS在插入语句时会检查插入操作是否破坏已定义的完整性规则

- 实体完整性
- 参照完整性
- 用户定义的完整性
  - `NOT NULL`约束
  - `UNIQUE`约束
  - 值域约束

