---
uthor: "Yiming Shen"
date: 2022-03-19
lastmod: 2022-03-19
title: "渗透测试记录"
tags: [
    "CTF",
    "Penetration",
    "Note",
]
categories: [
    "Notes",
     "CTF",
]
math: true
showtoc: true
tocopen: true
typora-root-url: ..\..\..\static\
---

> 本文主要记录渗透测试、挖洞的学习和实战经验。

## 逻辑越权

> 网站设计欠考虑

- **垂直越权**

  跨越权限访问

- **水平越权**

  同权限用户访问，通过更换身份标识，使A账号获取B账号数据

```
burpsuite -authz
```

### 垂直越权方法

> 前提条件：获取越权操作的数据包

1. 普通用户前端有操作界面可以抓取数据包
2. 通过网站源码本地搭建 模拟抓取
3. 盲猜

### 工具

1. `Secscan-authcheck`
2. `burpsuite -authz`

### 登录应用功能点安全问题

> 检测功能点、危害、修复方案

1. 登录点暴力破解
2. HTTP/HTTPS传输
3. Cookie脆弱点验证
4. Session固定点测试
5. 验证密文比对安全

### 支付漏洞

1. 替换支付
2. 重复支付
3. 最小额支付
4. 负数支付
5. 溢出支付
6. 优惠券支付

#### 常见篡改参数

1. 商品编号ID
2. 购买价格
3. 购买数量
4. 支付方式
5. 订单号
6. 支付状态
