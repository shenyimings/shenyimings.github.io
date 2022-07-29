---
author: "Yiming Shen"
date: 2022-03-10
lastmod: 2022-03-10
title: "CentOS 8.3 服务器配置"
description: "Lnmp、docker、syncthing"
tags: [
    "Tech",
    "Web",
    "Note",
    "Linux",
]
categories: [
    "Notes", 
    "PHP",
    "Technology",
]
math: true
ShowToc: true
draft: false
typora-root-url: ..\..\..\static\
---

## Readme

> 出于学习PHP等后端语言和搭建靶场的目的，我决定购置一台Linux服务器。(PHPStudy：那我走？)

本以为整个流程走下来又是废寝忘食的数天，结果格外顺利，仅用不连续的数小时就完成了系统配置和各个基本应用的安装，成功代理了hugo的静态站点，为了熟悉流程和记住常用命令，我用今天剩下的时间记录过程，以备未来查看。

## 购置云服务器、域名解析、备案

### 服务器选择

阿里云有学生机(开发者计划)，腾讯云有打折的轻量应用服务器，考虑到学生机常有而打折不常有，再加上我的域名也是在腾讯云上买的，便选择了腾讯云的轻量应用服务器(40￥一年)，系统选择了CentOS 8.3，没什么特别的原因，就感觉CentOS服务器都在用，也算是试图跳出常用ubuntu的舒适区一下吧。

### 域名解析

> 关于域名选择可以参考我的另一篇文章:[域名选择：有哪些很酷的域名？](https://shenyiming.life/post/essays/domain_choice/)

域名解析没什么特别之处，两个A记录分别指向www和@，另外在未来想设置多个二级域名用于博客、靶场、邮箱服务，也要通过A记录进行解析。

解析完并配置好lnmp后，网站立刻变成了提示备案……在备案完成前只能通过ip访问。

## 系统配置

### 1. SSH登录

> 有很多主机的root账户使用了诸如123456的弱密码，极其易被暴力破解抓作肉鸡，最好的方法是采用密钥登录。

在腾讯云服务器管理面板内创建密钥（类似Github)，获得一个私钥文件，务必妥善保存，将服务器绑定该密钥后，使用ssh连接软件配置载入私钥文件即可实现无密码登录root用户。

<img src="/serversettings.assets/%7D/image-20220310180935649.png" alt="image-20220310180935649" style="zoom:50%;" />

### 2. 安装并使用Screen

> Screen是一个可以在多个进程之间多路复用一个物理终端的全屏窗口管理器。Screen中有会话的概念，用户可以在一个会话中创建多个screen窗口，在每一个screen窗口中就像操作一个真实的telnet/SSH连接窗口那样。

安装Screen(CentOS)

`yum install screen`

#### 创建会话

```bash
screen -S lnmp
```

#### 查看会话

```bash
screen -ls
```

#### 关闭/切换会话

```bash
先Ctrl+A 再按D键，即可关闭当前会话（对进程无影响）

Ctrl+A 再按N或P，即切换到下一个/上一会话
```

#### 恢复会话

```bash
screen -r lnmp
```

> 有时在恢复screen时会出现There is no screen to be resumed matching \****，遇到这种情况咋办呢？输入命令
>
> `screen -d ****`

#### 退出会话

```bash
exit
```

### 3. 安装Lnmp

我使用了Lnmp一键安装包：

``` bash
screen -S lnmp

wget http://soft.vpser.net/lnmp/lnmp1.8.tar.gz -cO lnmp1.8.tar.gz && tar zxf lnmp1.8.tar.gz && cd lnmp1.8 && ./install.sh lnmp
```

安装时间较长，最后安装成功显示：

<p style="color: green">
Nginx:OK<br>
MySQL:OK<br>
PHP:OK<br>
</p>

#### Lnmp状态管理命令

LNMP 1.2+状态管理: `lnmp {start|stop|reload|restart|kill|status}`

LNMP 1.2+各个程序状态管理: `lnmp {nginx|mysql|mariadb|php-fpm|pureftpd} {start|stop|reload|restart|kill|status}`

Nginx状态管理：`/etc/init.d/nginx {start|stop|reload|restart}`

MySQL状态管理：`/etc/init.d/mysql {start|stop|restart|reload|force-reload|status}`

PHP-FPM状态管理：`/etc/init.d/php-fpm {start|stop|quit|restart|reload|logrotate}`

### 4. 安装Docker

```bash
sudo yum install docker-ce docker-ce-cli containerd.io
```

安装完成后 启动Docker

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

测试Docker是否安装正确 - 运行Helloworld

```bash
docker run --rm hello-world
```

## Syncthing配置

### 安装

安装Docker后，可直接拉取Syncthing镜像

```bash
docker pull syncthing/syncthing
```

### 运行

```bash
docker run -d -p 8384:8384 -p 22000:22000 -v /home/sync:/var/syncthing --restart=always syncthing/syncthing:latest
```

其中，`/home/sync:/var/syncthing`的意义是，将docker容器内部的虚拟存储挂载到外部的/home/sync文件夹内（映射）

通过理解挂载这一使用方式，可以很好的理解Docker的意义和实现方式。

### 配置

![image-20220310233831416](/serversettings.assets/%7D/image-20220310233831416.png)

Syncthing的运行和管理需要两个端口: `22000` (通信)`8384`（管理面板），需要在腾讯云管理面板的防火墙中打开这两个端口。

设置完成后，即可访问IP地址下的8384端口，进入syncthing的web管理面板

<img src="/serversettings.assets/%7D/image-20220310234101224.png" alt="image-20220310234101224" style="zoom:50%;" />

单击添加远程设备，即可连接本地电脑，单机添加文件夹即可共享文件夹。

> 我将本地电脑hugo生成的网站直接同步至服务器，这样做其实并不合适，在实际工作中生产环境和开发环境应该隔离分开。

## Nginx 配置

### 找到Nginx配置文件在哪个目录

```bash
nginx -t
```

![image-20220311000709044](/serversettings.assets/%7D/image-20220311000709044.png)

### 编辑`nginx.conf`文件

```nginx
server
    {
        listen 80 default_server reuseport;
        #listen [::]:80 default_server ipv6only=on;
        server_name _; # !!!! blog.shinning.cloud

        # index index.html index.htm index.php;

        root  /home/shenyiming.life/Shenyiming.Life;

        #error_page   404   /404.html;

        # Deny access to PHP files in specific directory
        #location ~ /(wp-content|uploads|wp-includes|images)/.*\.php$ { deny all; }

```

由于备案未完成，暂时无法配置二级域名和443端口https转发，主要是指定root目录在哪里。

## VScode 远程开发管理

> 配置好后发现，VScode才是yyds……觉得没必要把layout同步到服务器上了，直接本地连服务器开发编辑更方便。

1. 检查Windows是否安装OpenSSH

   > 在终端输入 ssh 即可检查

2. VScode安装Remote-SSH

   > 我的VScode似乎是自带的，只需要在设置中打开`Show Login Terminal`即可

3. 配置Config文件连接SSH

   > ```yaml
   > Host Shinning.cloud
   >     HostName 175.178.1xx.xx
   >     User 用户名，我是root
   >     IdentityFile C:\Users\xxx\.ssh\私钥文件
   > ```

<img src="/serversettings.assets/%7D/image-20220311000529720.png" alt="image-20220311000529720" style="zoom:67%;" />

点连接，即可愉快的编辑服务器文件啦~

## 参考

[1] [做好这两点，避免服务器成为肉鸡（傀儡）- 代码狂魔](https://zhuanlan.zhihu.com/p/56864040)

[2] [LNMP一键安装包](https://lnmp.org/install.html)

[3] [使用Syncthing搭建自己的私人网盘 - jonssonyan](https://zhuanlan.zhihu.com/p/471032477)

[4] [hugo博客部署到腾讯云轻量级服务器 - sulv's blog](https://www.sulvblog.cn/posts/blog/hugo_deploy/)

[5] [在Nginx中配置二级域名 - 码农小黄](https://mincong.io/cn/nginx-subdomains/)

[6] [使用VScode连接远程服务器进行开发 - 这里有支彩笔](https://zhuanlan.zhihu.com/p/141205262)

[7] [linux screen的用法](https://www.jianshu.com/p/e91746ef4058)

[8] [Docker 入门教程 - 阮一峰](https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
