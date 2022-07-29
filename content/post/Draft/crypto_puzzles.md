---
author: "Yiming Shen"
date: 2022-03-03
lastmod: 2022-03-03
title: "Crypto题目"
tags: [
    "Puzzle",
    "CTF",
]
categories: [
    "CTF", 
]
# weight: 1
math: true
ShowToc: false
draft: true
typora-root-url: ..\..\static\
---

## 1

```
Y3NldHRfZl9jc2FyaF95b3Nwd2l0JTdCbW9qcCF1bCU3RA==

base64解码后得到：

csett_f_csarh_yospwit%7Bmojp!ul%7D

urldecode以后得到：
csett_f_csarh_yospwit{mojp!ul}

栅栏解密以后得到：
csett_f_csarh_yospwit{mojp!ul}

2栏：
cosseptwti_tf{_mcosjapr!hu_ly}

3栏：
catsr{ehmt_otyj_opfs!_pucwlsi}

5栏：
cfhwjs__ipecyt!tso{utasml_rpo}

6栏：
c_aotpsfrs{!e_hpmutc_woltsyij}

10栏：
ctfshow{just_a_simple_crypto!}

15栏：
cetfcahyswtmj!lst__sr_opi{opu}
```



```
HDCTF{simp1e_crypto}

Railfence:hrdcyc_ptetf1o{p}smi

Caesar:ajruonwln:qamlhl_ycnco1x{y}bvr

Caesar%3Aajruonwln%3Aqamlhl_ycnco1x%7By%7Dbvr

436165736172253341616a72756f6e776c6e25334171616d6c686c5f79636e636f317825374279253744627672

```



文件经过rot13编码，使用winhex进行rot13转换后得到Rar！开头的文件，补上后缀名，解压得到文件。

如题，是个共享秘密方案，考虑到可能是基于中国剩余定理的秘密共享方案[(基于中国剩余定理的秘密共享方案_Y0uth-的博客-CSDN博客](https://blog.csdn.net/qq_43375921/article/details/121390019)，得到一串十进制数然后经过ascii解码得到一个明显为经过base编码的值，base64解码可得到flag

```python
import copy
import base64
from Crypto.Util.number import long_to_bytes


def init(Data):  # 对数据进行初始化，X中存储待模数，Y中存储模数
    X = []
    Y = []
    for i in range(len(Data) // 2):
        X.append(Data[i])
        Y.append(Data[i + len(Data) // 2])
    return X, Y


def inverse(a, b):  # x为待模数，y为模数 求逆元
    if b == 0:
        return 1, 0
    else:
        x, y = inverse(b, a % b)
        return y, (x - (a // b) * y)


def create_M(Y):  # 建立对应数的其它数的乘积
    M = []
    temp = 1
    for i in Y:
        temp = i * temp
    for k in Y:
        M.append(temp // k)
    return M, temp


def check(li):  # 确认数之间是否两两互素
    check_li = copy.deepcopy(li)
    for i in check_li:
        check_li.remove(i)
        for j in check_li:
            p, q = i, j
            while p % q != 0:
                p, q = q, p % q
            if q != 1:
                return 0
            else:
                pass
    return 1


def solve(T, X, M):  # 求解函数 T中是对应的模逆元的列表，X中是对应的模数
    result = 0
    for num in range(len(T)):
        i, j, m = T[num], X[num], M[num]
        result += i * j * m

    return result


def crt_funtion(r_data):
    X, Y = init(r_data)
    if check(Y) == 0:
        pass
    else:
        M, multi_m = create_M(Y)
        INVERSE = []
        for s in range(len(M)):
            m, y = M[s], Y[s]
            T_m, T_y = inverse(m, y)
            INVERSE.append(T_m % y)
        result = solve(INVERSE, X, M) % multi_m
        return result


with open('seekflag.txt','r') as f:
    data = f.readlines()
    k = []
    d = []
    for i in data:
        if ':KEY:' in i:
            k.append(eval(i.split(':KEY:')[1]))
        elif ':D:' in i:
            d.append(eval(i.split(':D:')[1]))


solved_crt = crt_funtion(k + d)
hex_f = long_to_bytes(solved_crt)
flag = base64.b64decode(hex_f).decode()
print("明文是：" + flag)
```

flag是HDCTF{HNUCTF_HeRe_1s_true_f1a9}

