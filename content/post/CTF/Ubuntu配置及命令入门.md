---
author: "Yiming Shen"
date: 2021-07-20
lastmod: 2021-07-20
title: "Ubuntu 18.04 LTS的配置与入门"
tags: [
    "CTF",
    "Tech",
    "Note",
]
categories: [
    "Notes",
     "CTF",
"Technology",
]
math: true
typora-root-url: ..\..\..\static\
---

---

### 系统选择

Linux有多种发行版，作为一名小白，我选择Ubuntu的原因是以前用过并且感觉桌面界面十分讨喜，再者，Ubuntu作为常见的个人桌面操作系统，使用人数相对较多，教程也更多，对萌新十分友善。

### **载体**

最标准的方式当然是重装系统咯，总是佩服小时候爱捣鼓的自己，想格盘就格盘，说重装就重装了，长大以后，不知是懒了还是如何，很难再去断舍离了，只能继续看着又快满的C盘抱残守旧了，单系统不可取，双系统也要重新给硬盘分区，过于麻烦，那就传统的VMware虚拟机吧，在去Ubuntu官网下载镜像时，发现了一个全新的选择：Windows WSL，即Windows Subsystem for Linux，籍此可以直接在Windows 10上运行Ubuntu，安装便捷，启动快速，就决定是你了！

### **系统配置**（学习网址：CSDN博客）

1. 安装好后首先设置root用户, 更换apt数据源

```shell
sudo passwd root
su root
cd /etc/apt
cp sources.list sources.list.bak #备份配置文件
vim sources.list #编辑配置文件
```

2. 将配置文件内容更改为阿里云镜像源

```shell
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

VIM基本基本基本命令：按i进入插入模式，进行编辑；按esc回到标准模式，输入

`:wq`保存并退出

3. 安装Python 3.9

   `sudo apt update
   sudo apt install software-properties-common`
   1、使用Apt-Get安装Python 3.9
   Apt软件包管理器提供了在Ubuntu系统上安装Python 3.9的简单方法。请按照以下步骤操作：
   打开系统上的终端，然后为系统配置Deadsnakes PPA。
   `sudo add-apt-repository ppa:deadsnakes/ppa`
   在Ubuntu系统上添加ppa后，更新apt缓存并在Ubuntu上安装Python 3.9。
   `sudo apt update
   sudo apt install python3.9`
   等待安装完成。通过执行以下命令检查Python版本：
   `python3.9 -V`

至此，一个船新的Ubuntu 18.04 LTS 便配置完毕。