---
author: "Yiming Shen"
date: 2022-05-05
lastmod: 2022-05-06
title: "微信小程序云开发学习"
tags: [
    "小程序",
    "Web",
    "Tech",
    "Note",
]
categories: [
    "Notes", 
    "Techs",
    "PHP",
]
math: true
ShowToc: true
draft: false
typora-root-url: ..\..\..\static\
---

## 云开发概述

1. 云函数  （定时触发）
2. 云数据库 （Json数据库）
3. 云调用  （调用腾讯云服务）
4. HTTP API  （对接第三方服务）
5. 云存储  （放置项目文件）

## 云开发模式

小程序端 -> 云数据库、云函数、云存储 -> 云调用

Serverless 无服务开发

函数即服务

### 优势

- 快速上线
- 专注核心业务
- 独立开发一个完整的微信小程序
- 无需运维，节约成本
- 弹性伸缩
- 数据安全

## 课程

#### 多行文本省略：

```css
.class{
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 2;
    overflow: hidden;
    text-overflow: ellipsis;
}
```

#### 组件监听器：

```javascript
observers:{
    ['playlist.playCount'](val){
        console.log(val)
    }
}
```

> 不要直接对正在监听的值进行赋值，会导致死循环

#### 同步与异步

> 有些操作需要依赖于上一步操作，则需要同步操作

##### Promise

1. pending
2. fulfilled
3. rejected

```javascript
new Promise((resolve,reject)=>{
    setTimeout(()=>{
        console.log(1)
        resolve()
    },1000)
}).then((res)=>{
    setTimeout(()=>{
        console.log(2)
    }，2000)
})
}）
// 按顺序执行p1 p2 p3
Promise.all([p1,p2,p3]).then((res)=>{
    console.log()
}).catch((err)=>{
    console.log()
})
// 按完成时间先后顺序执行p1 p2 p3
```

##### async await （ES7）

