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
---

 ## Less- 1 SELECT 查询

### 数据库

数据库由若干张`表`(Table)组成，这里说的数据Table很像Excel里的表; 正如Excel里的表格，Table也是由 `行(rows)`和`列(columns)`组成

### SELECT

*`SELECT 查询属性列`*

```sql
SELECT column1, column2,... FROM _table;
```

查询结果为一个行列组成的二维表格。

*用\*号代替所有列名*

```sql
SELECT * FROM _table;
```

查询结果为整个数据库（表）



## Less-2 条件查询

