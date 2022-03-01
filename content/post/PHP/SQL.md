---
author: "Yiming Shen"
date: 2022-02-28
lastmod: 2022-02-28
title: "SQL Tutorial"
description: "Database&SQL"
tags: [
    "Web",
    "SQL",
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
cover: 
    image: "/SQL.assets/%7D/5863241277243392.png"
---

## 数据库

数据库由若干张`表`(Table)组成，这里说的数据Table很像Excel里的表; 正如Excel里的表格，Table也是由 `行(rows)`和`列(columns)`组成

### RDBMS 数据库管理系统

- **数据库:** 数据库是一些关联表的集合。
- **数据表:** 表是数据的矩阵。在一个数据库中的表看起来像一个简单的电子表格。
- **列:** 一列(数据元素) 包含了相同类型的数据, 例如邮政编码的数据。
- **行：**一行（=元组，或记录）是一组相关的数据，例如一条用户订阅的数据。
- **冗余**：存储两倍数据，冗余降低了性能，但提高了数据的安全性。
- **主键**：主键是唯一的。一个数据表中只能包含一个主键。你可以使用主键来查询数据。
- **外键：**外键用于关联两个表。
- **复合键**：复合键（组合键）将多个列作为一个索引键，一般用于复合索引。
- **索引：**使用索引可快速访问数据库表中的特定信息。索引是对数据库表中一列或多列的值进行排序的一种结构。类似于书籍的目录。
- **参照完整性:** 参照的完整性要求关系中不允许引用不存在的实体。与实体完整性是关系模型必须满足的完整性约束条件，目的是保证数据的一致性。

![img](/SQL.assets/%7D/0921_1.jpg)

 ## Less- 1 SELECT 查询



### SELECT

*SELECT 查询属性列*

```sql
SELECT column1, column2,... FROM _table;
```

查询结果为一个行列组成的二维表格。

*用\*号代替所有列名*

```sql
SELECT * FROM _table;
```

查询结果为整个数据库（表）



## Less-2 条件查询-1

以表中的某列名为条件，返回符合条件行(rows)的结果

*条件查询语法*

```sql
SELECT column1, column2,... FROM _table where condition1 AND/OR condition2;
/*condition 描述属性*/
```

| Operator（关键字）  | Condition（意思）                                            | SQL Example(例子）            |
| ------------------- | ------------------------------------------------------------ | ----------------------------- |
| =, !=, < <=, >, >=  | Standard numerical operators 基础的 大于，等于等比较         | col_name != 4                 |
| BETWEEN … AND …     | Number is within range of two values (inclusive) 在两个数之间 | col_name BETWEEN 1.5 AND 10.5 |
| NOT BETWEEN … AND … | Number is not within range of two values (inclusive) 不在两个数之间 | col_name NOT BETWEEN 1 AND 10 |
| IN (…)              | Number exists in a list 在一个列表                           | col_name IN (2, 4, 6)         |
| NOT IN (…)          | Number does not exist in a list 不在一个列表                 | col_name NOT IN (1, 3, 5)     |

### Exercise

| Id   | Title               | Director       | Year | Length_minutes |
| ---- | ------------------- | -------------- | ---- | -------------- |
| 1    | Toy Story           | John Lasseter  | 1995 | 81             |
| 2    | A Bug's Life        | John Lasseter  | 1998 | 95             |
| 3    | Toy Story 2         | John Lasseter  | 1999 | 93             |
| 4    | Monsters, Inc.      | Pete Docter    | 2001 | 92             |
| 5    | Finding Nemo        | Finding Nemo   | 2003 | 107            |

1. 找到id为6的电影

```sql
SELECT * FROM movies where id =6;
```

2. 找到2000-2010间上映的电影

```sql
SELECT * FROM movies where year between 2000 and 2010;
```

3. 找到不在2000-2010间上映的电影

```sql
SELECT * FROM movies where year not between 2000 and 2010;
```

4. 找到头5部电影

```sql
SELECT * FROM movies where id<=5;
```

5. 找到2010（含）年之后的电影里片长小于两个小时的片子

```sql
SELECT * FROM movies where year>=2010 and Length_minutes<120;
```

## Less-3 条件查询-2

之前学习了使用where筛选数字类型属性，若属性是字符串，则会用到下面的操作符号。

| Operator（操作符） | Condition（解释）                                            | Example（例子）                                              |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| =                  | Case sensitive exact string comparison (*notice the single equals*)完全等于 | col_name = "abc"                                             |
| != or <>           | Case sensitive exact string inequality comparison 不等于     | col_name != "abcd"                                           |
| LIKE               | Case insensitive exact string comparison 没有用通配符等价于 = | col_name LIKE "ABC"                                          |
| NOT LIKE           | Case insensitive exact string inequality comparison 没有用通配符等价于 != | col_name NOT LIKE "ABCD"                                     |
| %                  | Used anywhere in a string to match a sequence of zero or more characters (only with LIKE or NOT LIKE) 通配符，代表匹配0个以上的字符 | col_name LIKE "%AT%" (matches "AT", "ATTIC", "CAT" or even "BATS") "%AT%" 代表AT 前后可以有任意字符 |
| _                  | Used anywhere in a string to match a single character (only with LIKE or NOT LIKE) 和% 相似，代表1个字符 | col_name LIKE "AN_" (matches "AND", but not "AN")            |
| IN (…)             | String exists in a list 在列表                               | col_name IN ("A", "B", "C")                                  |
| NOT IN (…)         | String does not exist in a list 不在列表                     | col_name NOT IN ("D", "E", "F")                              |

类似正则表达式

**字符串需要用双引号包含，不包含将被认作列名**

### Exercise

> 在上一章的表中完成以下任务

1. 【复杂条件】找到所有`Toy Story`系列电影

   ```sql
   SELECT * FROM movies where title like "%Toy Story%";
   ```

   

2. 【复杂条件】找到所有`John Lasseter`导演的电影

   ```sql
   SELECT * FROM movies where director like "%John%";
   ```

   

3. 【复杂条件】找到所有不是`John Lasseter`导演的电影

   ```sql
   SELECT * FROM movies where director not like "%John%";
   ```

   

4. 【复杂条件】找到所有电影名为` "WALL-" `开头的电影

   ```sql
   SELECT * FROM movies where title like "WALL-%";
   ```

   

5. 【复杂条件】有一部98年电影中文名《虫虫危机》请给我找出来

   ```mysql
   SELECT * FROM movies where year=1998;
   ```

   

## Less-4 查询结果处理

`DISTINCT`  用于指定某个或某些属性列返回唯一值

```sql
SELECT DISTINCT column1,column2, ... FROM _table where condition1;
```

