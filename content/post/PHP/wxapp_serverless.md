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
    "Technology",
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

### 云函数

#### 调用数据库

```javascript
const cloud = require('wx-server-sdk')
cloud.init()
const db = cloud.database()
const pdb = db.collection('playlist').get()
// 初始化
exports.main = async (event,context)=>{
    
    await db.collection ('posts').add({
        data:{
            ...context, // 插入每一条数据
            createTime: db.serverDate(),
        }
    }).then((res)=>{
        console.log('插入成功')
    }).catch((err)=>{
        console.error('插入失败')
    })
}
```

#### 数据去重

双重循环比对

#### 突破数据条数限制

核心代码

```javascript
for (let i = 0; i<batchTimes; i++){
    let promise = playlistCollection.skip(i*MAX_LIMIT).limit(MAX_LIMIT)
    tasks.push(promise)
}
list = (await Promise.all(tasks)).reduce((acc,cur) =>{
    return{
        data: acc.data.concat(cur.data)
    }
})
```

#### 定时触发云函数

在`config.json`中

```javascript
{
    "triggers": [
        {
            "name": "myTrigger",
            "type": "timer",
            "config": "0 0 10,14,16 * * * *"
        }
    ]
}
```

每天10点 14点 16点执行

**需要手动上传触发器**

#### 数据库操作

**云函数：**

```javascript
exports.main = async (event,context) =>{
    return await cloud.database().collection('posts')
    .skip(event.start).limit(event.count)
    .orderBy('createTime','desc')
    .get()
    .then((res) => {
        return res
    })
}
```

**前端**

```javascript
onLoad (options){
    wx.cloud.callFunction({
        name: 'getPosts',
        data: {
            start: this.data.posts.length,
            count: 15
        }
    }).then((res)=>{
        this.setData({
            posts: res.result.data
        })
    })
}
```

### 数据结构

数据 -> 云数据库

云数据库 -> 内容、图片fileID、用户openID、昵称、头像URL、时间

图片 -> 云存储 -> 得到fileID
