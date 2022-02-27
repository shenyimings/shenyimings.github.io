---
author: "Yiming Shen"
date: 2021-08-04
lastmod: 2021-09-01
title: "Windows11体验报告：你可以永远相信微软的审美"
tags: [
    "Windows",
    "Tech",
    "Essay",
    "Idea",
]
categories: [
    "Essays", 
    "Technology",
    "Ideas",
]
math: true
ShowToc: true
draft: false
cover:
    image: https://pic1.zhimg.com/v2-3d39b77daca514c6f051d98096855989_1440w.jpg
typora-root-url: ..\..\..\static\
---

## 前言

Windows11从dev版本进入beta版本后，我便决定加入预览者大军，亲眼看看号称Windows10是最后一版Windows之后的微软搞出了怎样一个套壳系统。

### 配置

>Intel I5 9400F/RX580 2048SP/16G/256G SATA SSD+512G NVME SSD 

### 安装

1. 安装Windows11，首先要确定设备是否开启了TPM2.0。

   Win+R 输入`tpm.msc`，若显示TPM已就绪，可以使用，则已开启TPM2.0。若未开启，可进入主板BIOS界面，在高级选项卡中，Intel 芯片组找`PTT`设置为`enable`，AMD芯片组找`AMD ftpm`设置为`enable`即可，具体教程可搜索*XX主板开启TPM2.0*，文章不再赘述。

![img](/Windows11.assets/%7D/v2-c3061f96b379dafd49e5e833ef87b196_720w.jpg)

2. 开启TPM2.0 后，基于微软官方已给出了预览计划提前升级安装的方案，不必再进行全新安装。故进入设置-Windows更新-Windows预览体验计划，加入beta版本预览体验后，重启，再次进入Windows更新，即可获得Windows11 beta版。

## 使用报告

1. 界面很惊艳，特别像Ubuntu和MAC风（趋势，趋势，不是抄袭）

2. 任务栏全部居中，和我之前用Windows10时用FalconX更改的一致，偏向于Mac系统的dock栏方向。

3. 全新的UI、开机声音、交互音效。

4. 暂未遇到应用兼容性问题，果真是Windows10套壳#滑稽

5. 右键菜单加入了`在Windows终端中打开`命令，向Linux系统靠拢。复制粘贴剪切命令变成了小图标，右键菜单部分非系统命令被折叠，如压缩解压文件命令，十分不便，正在养成用Shift+F10命令直接展开菜单的习惯。目前网上也有提供regedit改回原菜单的方法。

6. 设计语言不统一，这个主要是应用的锅，还没有进行Windows11适配，导致圆角和直角并存，群魔乱舞。很有意思的一点是窗口占满全屏或贴边后会从圆角变为直角。

   ![image-20210804105643660](/Windows11.assets/%7D/v2-74d8a53ec37ac78babdb9e5327144e25_720w-16444113080344.jpg)

7. WSL和Windows终端的配合体验极佳。最新版的WSL2支持GPU，允许直接在Windows系统运行基于Linux的图形界面，如上图运行着基于Ubuntu的Chrome 64。Windows11现在已经可以直接在应用商店安装运行少数Android应用了，只是目前国内应用发行商适配极少。果然，Windows11才是Linux最好的发行版，实锤了。

   ![image-20210804104403441](/Windows11.assets/%7D/v2-211a99eb1ed8fb2fd34c119f3b7cc560_720w.jpg)

8. 开始菜单安卓化，取消了磁贴，应用一屏放不下，且个性化空间较小，十分类似安卓的抽屉。

![image-20210804114403906](/Windows11.assets/%7D/v2-afde1edd3d00608276fb207f1a37f7a9_720w.jpg)

9. 应用商店可用性比以前大大提高，更加人性化，下载安装速度加快，不再是花架子了。
10. Windows defender用原来组策略修改的方法关不掉了…… DefenderControl也只是关的了一时，这对满电脑后门脚本修改器破解补丁的我造成了很大的困扰。

## BUG

![image-20210804123548946](/Windows11.assets/%7D/v2-420609ede194a6d71fe6fae0f16cb14a_720w.jpg)

1. 资源管理器经常出现莫名的卡顿，具体表现为鼠标操作缓慢，文件拖拽迟缓，鼠标错位/出现两个鼠标指针
2. 圆角直角同时存在，设计语言不统一。

我在两天的使用过程中，暂未碰到网友反映的游戏掉帧、SSD速度变慢、系统内存占用高的问题，考虑到以上原因切实存在，以及有人说AMD CPU存在性能变差的问题，建议等待正式版再行更新。





## 参考

- [1] [Cnbeta](https://www.cnbeta.com/articles/tech/1145233.htm)
- [2] [知乎-三无小号](https://www.zhihu.com/question/470149297)