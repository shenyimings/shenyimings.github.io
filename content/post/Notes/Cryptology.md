---
author: "Yiming Shen"
date: 2022-05-24
lastmod: 2022-06-02
title: "应用密码学复习提纲"
tags: [
   "Cryptology",
    "review",
    "Note",
]
categories: [
    "Notes", 
"Courses",
]
math: true
ShowToc: true
draft: false
typora-root-url: ..\..\..\static\
---

## 前言

> 作者：申一鸣
>
> 创建日期：2022/5/24
>
> 最后更改：2022/6/2

## 提纲

[TOC]

## 基本概念

### CIA三元组

- 保密性
- 完整性
- 可用性

### OSI安全框架

- 安全攻击
- 安全机制
- 安全服务

## 古典密码

> 古典密码是指通过单表替代或置换生成的，与体系化运用数学手段建模的现代密码学相对应的密码。

*古典密码学此处只介绍仿射密码，其余省略，具体可参考[^CTF-Wiki]中对古典密码的介绍*

### 仿射密码 Affine

\*广义凯撒密码，简单的基于有限域的密码
$$
明文m,密文c,参数a,b\in Z_{26}\\\\
加密： e(m)=c\equiv a\cdot m+b (mod\quad 26)\newline
特别地，当a=1,b=k时，为凯撒密码。\\\\
解密：d(c)=m\equiv a^{-1}\cdot(c-b)(mod\quad 26)
$$
**求逆元$$a^{-1}$$ 扩展欧几里得算法 Exgcd**

由逆元的定义决定了，必须满足以下条件：
$$
gcd(a,26)=1,即a与26互质\\\\
a\in\{1,3,5,7,9,11,15,17,19,21,23,25\}
$$


## 分组密码

> 分组密码是一种加/解密算法，将输入的明文分组当作一个整体处理，输出一个等长的密文分组。

### Feistel 密码结构

> 对理想分组密码的近似

![image-20220528001126847](Cryptology.assets/%7D/image-20220528001126847.png)

使用**乘积密码**增强密码强度，交替使用代换和置换，增强密码的**扩散**和**混淆**性能。

**密钥和密文关系为非线性关系**

**混淆（Confusion）**：使密钥与密文的关系尽可能模糊，使用替换法达到目的。

**扩散（Diffusion）**：隐藏明文的统计特性，使用位置换达到目的。

#### 优点：

- 对称密码结构，加密解密过程几乎一样
- 编码量和线路传输的要求少了几乎一半

### Feistel 密码结构

- 分组长度
  - 分组越长代表安全性越高（扩散性能越好），但会降低加解密速度。
- 密钥长度
  - 密钥越长安全性越高（抗穷举、提高混淆）
- 迭代轮数
  - 多轮加密可取得较高的安全性

**Feistel密码通常由以下步骤组成**

1. 子密钥生成算法
2. 轮函数
3. 快速软件加解密
4. 简化分析难度

### 数据加密标准 DES

![img](https://www.ruanx.net/content/images/2020/04/figure2-des_block.gif)

![img](https://images2018.cnblogs.com/blog/1281268/201807/1281268-20180720073949486-1474910065.png)

![img](https://www.ruanx.net/content/images/2020/04/image-2.png)



- 算法分为两部分：**迭代加密**、**密钥调度**
- **以下内容摘自阮行止Pion1er大佬博客[^ruanx.net]**

　　所谓密钥调度，就是**从一把 64-bit 的主钥匙，得到 16 把 48-bit 的子钥匙**，然后把这些子钥匙用于迭代加密。那么，如何从一把主钥匙得到 16 把子钥匙呢？

1. 从 64-bit 的主钥匙里面选取特定的 56 位，其余的位就没用了。于是我们现在手上有了一个 56 位的布尔数组。把它分成左、右两个半密钥，它们都是 28-bit 的布尔数组。
2. 左、右两个半密钥都左旋（也就是循环左移。整个数组往左移，左边弹出去了的东西补到最右边去）一定位数，这个左移的位数也是指定的。有些轮次是 1 位，有些轮次是 2 位。
3. 把左、右半密钥拼起来，再做一个置换，就得到了这一轮生成的子密钥。这个置换是从 56-bit 的数组里面选取指定的 48 位。所以现在每一轮都可以生成一个 48 位的子密钥。（注意，步骤 3 并不改变左右半密钥）。
4. 重复 步骤 2、步骤 3 一共 16 次，于是得到了 16 个 48-bit 的子密钥。

现在我们手上有了 16 把子密钥。遂开始加密：

1. 输入的明文（长度为 64 的布尔数组）做一个置换(IP置换)。仍然得到 64-bit 的数组（不然就丢失信息了！）

2. 把得到的数组拆成左、右两半边。每边是 32 位长度。

3. 每一轮迭代，都是接收一组 `L, R`，返回 `L', R'` ，作为下一轮迭代的 `L, R` 迭代过程如下：
   
$$
L'=R\\\\
R'=L\oplus F(R,subkey)
$$

   其中 F 函数（称为轮函数）是整个算法的核心，功能是：以一个子密钥，加密 32-bit 的信息。

4. 利用之前得到的 16 个子密钥，执行步骤 3 一共 16 次。

5. 将最终的 R 与 L 拼接，再做一次置换(FP置换)，即得到密文。

Feistel轮函数：

1. 扩展32位R至48位
2. 轮密钥XOR异或
3. S盒置换（8个S盒，每盒输入6位，输出4位）
4. P置换

> 一个 32-bit 的块，经过一个扩张(Expand函数)，变成 48 位，然后**与子密钥异或**。得到的 48-bit 的结果分为 8 组，每一组是 6-bit 的数据，丢进对应的 **S 盒**，输出 4-bit 的信息。把这些输出收集起来，一共是 4*8 = 32 位，做一次置换 (P 置换)，得到 32-bit 的结果。这与输进来的 32-bit 信息是等长度的。

S盒：

- S盒是对称密码系统分组加密算法中最核心的安全部件（抗**差分密码攻击**）
- 6输入中的第1和第6比特组成的二进制数确定的行，中间4位二进制数确定列
- 相应位置的十进制数转为4位二进制数进行输出



> **雪崩效应**：明文或密钥的某一位发生变化（微小的变化）将对密文的结果产生很大的影响。

#### DES的工作模式

1. **电子编码本 ECB**
2. **密码分组链接 CBC**
3. 输出反馈 OFB
4. 密码反馈 CFB

### 高级加密标准 AES

![整体结构](https://img-blog.csdnimg.cn/6c9010fc15894c8ab6c49850e8cc93ea.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAYnVndW_opb_nk5w=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

![密钥扩展](https://img-blog.csdnimg.cn/7b36bee2d1974ecb97a3cc25e09a8b75.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAYnVndW_opb_nk5w=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

（AES不是Feistel结构，是每一轮并行的处理每一个分组）

密钥长度：128位

流程：

1. 字节代换
2. 行移位
3. 列混合
4. 轮密钥加



### 有限域运算[^有限域上的四则运算]

#### 加法和减法：

加法即异或运算
减法即加法              

#### 乘法和除法：

伽罗华域上的多项式乘法，其结果需要mod P(x)，可以通过以下方式简化计算。

首先，考虑x^8，x^8 mod P(x) = P(x) – x^8 = x^4 + x^3 +x^2 +1 。

　　对于一般形式的多项式f(x)=a7*x^7 + a6*x^6 + a5*x^5 + a4*x^4 + a3*x^3 + a2*x^2 + a1*x + a0，乘以x得到

$$
x*f(x) = (a7*x^8 + a6*x^7 + a5*x^6 + a4*x^5 + a3*x^4 + a2*x^3 + a1*x^1 + a0*x) mod P(x)
$$

**这时有两种情况：**
**1）a7 == 0，此时结果是一个小于指数小于8的多项式，不需要取模计算。**
**2）a7 == 1，则将x^8替换为x^4 + x^3 + x^2 +1，而不用进行除法取模计算，结果为：**
**x\*f(x) = (x^4 + x^3 + x^2 +1) + a6\*x^7 + a5\*x^6 + a4\*x^5 + a3\*x^4 + a2\*x^3 + a1\*x^1 + a0\*x** 

![乘法](https://img-blog.csdnimg.cn/b6857bd9511343cb8ab5113d71c945cc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAYnVndW_opb_nk5w=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

$$
m(x)=x^8+x^4+x^3+x+1\\\\
x^8\equiv x^4+x^3+x+1 (mod\quad m)\\\\
x^9\equiv x*x^8=x^5+x^4+x^2+x ...依次类推
$$


## 公钥密码学与RSA

> **陷门单向函数**
>
> - 在不知陷门信息下，由f(x)求x极为困难
> - 当知道陷门信息后，由f(x)求x是易于实现的
>
> (与单向函数的区别是 单向函数都极为困难)

### 公钥密码学解决的基本问题

- 密钥交换
  - 要求：已经共享了一个密钥，或利用密钥分配中心
- 数字签名

### 公钥密码体制

#### 组成部分

明文 密文 公钥 私钥

加密 解密算法

#### 安全性

任何加密方法的安全性依赖于密钥的长度和破译密文所需要的计算量，不能说公钥密码优于传统密码

#### 意义

解决了传统密码中最困难两个问题

- 密钥分配
- 数字签名

### RSA 算法

> 明文和密文是0到n-1之间的整数，n的大小为1024位或309位十进制数。

#### 流程

$$
随机选择两个大素数p,q\\\\
计算N=p\cdot q,\phi(N)=(p-1)(q-1)\\\\
选择e,使得1<e<\phi(N),gcd(e,\phi(N))=1\\\\
e\cdot d\equiv 1(mod\quad \phi(N))且0\le d\le N\\\\
d \equiv e^{-1}(mod\quad N)\\\\
公钥KU=\{e,N\}\\\\
私钥KR=\{d,p,q\}\\\\
密文C = M^e(mod\quad N)\\\\
明文M = C^d(mod\quad N)\\\\
$$

```python
def EX_GCD(a,b,arr): #扩展欧几里得
    if b == 0:
        arr[0] = 1
        arr[1] = 0
        return a
    g = EX_GCD(b, a % b, arr)
    t = arr[0]
    arr[0] = arr[1]
    arr[1] = t - int(a / b) * arr[1]
    return g
def R(a,n): #ax=1(mod n) 求a模n的乘法逆x
    arr = [0,1,]
    gcd = EX_GCD(a,n,arr)
    if gcd == 1:
        return (arr[0] % n + n) % n
    else:
        return -1
```

### 对RSA 算法的攻击

#### 直接攻击

1. 分解N
2. 获取φ(N)，确定d
3. 直接确定d

**已经证明，若$$e<n,d<n^{\frac{1}{4}}$$,则d很容易被确定**

#### 共模攻击

> 在模数N不变的情况下，已知两个生成密钥e1、e2和用其分别加密的相同m信息c1、c2和模数N，可以在不知道d1、d2的情况下解出明文m

使用扩展欧几里得Exgcd算法，求出
$$
e_1\times s_1+e_2\times s_2 = 1
$$
中的$$s_1和s_2$$

即可得
$$
m = c_1^{s_1}\times c_2^{s_2}
$$
证明略，具体可参考[^RSA共模攻击]

#### 计时攻击

> 通过观察系统处理上述函数所花费的时间来判断，很慢的指数比特为1，否则为0，如此即可得到整个指数。

##### 避免计时攻击

1. 不变的运算时间-会降低算法性能
2. 随机延时，扰乱分析
3. 执行幂运算前先将密文乘上一个随机数，使攻击者不知道正处理密文的哪些位。

#### 消息隐匿失败-不动点问题

对于明文x，使用RSA加密，可能出现
$$
x^e\equiv x(mod\quad n)
$$
对于RSA算法而言，总存在x=0,1,(n-1)为不动点

除此之外，还存在
$$
[1+gcd(e-1,p-1)\times [1+gcd(e-1,q-1)]]
$$
个不动点，至少有3*3=9个



### Diffie-Hellman 密钥交换

> 1976年由Diffie和Hellman提出的第一个实用的密钥公开交换算法。

基于离散对数的求解困难问题

#### 实现

$$
公开选定公钥:(g,p),其中p是一个质数,g是它的一个原根\\\\
Alice想一个随机数x并计算A=g^x，将A发送给Bob\newline
Bob想一个随机数y并计算B=g^y，将B发送给Alice\\\\
Alice和Bob接到A、B后,密钥K=B^x=A^y
$$

如此，即可在不安全的通信信道中进行安全的密钥交换。

#### 中间人攻击

中间人Darth将自己生成的随机数，用Alice的公钥生成自己的密钥，传递给Bob，接收并解密Bob发送的消息。

![image-20220531150129990](/Cryptology.assets/%7D/image-20220531150129990.png)

### ElGamal 加密算法

> D-H算法解决了密钥交换的问题，但密钥不带有信息，无法直接通讯，ElGamal公钥加密算法解决了通讯的问题，安全性与D-H算法一致。

####  实现

$$
Alice随机数a作为私钥;选取g、p，A=g^a作为公钥\\\\
Bob想给Alice传递信息m，选取随机数k计算\newline
K=A^k(mod\quad p)\qquad c_1=g^k(mod\quad p),c_2=mK(mod\quad p)\\\\
Alice拿到(c_1,c_2)，计算\newline
K=c_1^{a}\qquad m=c_2K^{-1}=c_1^{-a}\cdot c_2
$$

**注意与ElGamal数字签名生成和验证算法的异同**

#### 练习

在mod 17,g=3的情况下验证ElGamal加解密方案
$$
Alice私钥a=2公钥(g=3,p=17,A=g^a(mod\quad p)=4)\\\\
Bob消息m=6,b=3,K=A^b=13,c_1=g^b=10,c_2=mK=5\\\\
Alice收到c_1,c_2,解密m=10^{-2}\cdot 5=6
$$

#### 缺点

- 需要随机数
- 密文长度加倍（两倍消息扩张）

## Hash函数

> Hash函数是一个将任意长度的消息序列映射为较短的、固定长度的一个值的函数，用于构造数据的指纹，保障数据的完整性。

Hash函数是一个公开函数，将任意长的消息M映射为较短的、固定长度的一个值H(M)
$$
Y=H(M),\{0,1\}^*\rightarrow\{0,1\}^n
$$

| 需求           | 描述                                     |
| -------------- | ---------------------------------------- |
| 输入长度可变   | H可应用于任意大小的数据块                |
| 输出长度固定   | H产生定长输出                            |
| 效率           | 计算H(x)较容易                           |
| 单向性         | 逆推x不可行                              |
| **抗弱碰撞性** | 对于给定的分块，碰撞到相同的Hash值不可行 |
| **抗强碰撞性** | 碰撞到任何相同的Hash值不可行             |
| 伪随机性       | 输出满足伪随机性测试标准                 |

### 应用

#### 消息认证

> 用于验证消息完整性时，Hash函数值被称为**消息摘要**

消息认证是通过MAC，即带密钥的哈希函数实现的

#### 数字签名

- 提高签名速度，只对摘要签名
- 不泄露签名所对应的消息
- 签名与加密分别处理

#### 其他

- **创建单向口令文件**，即只存储口令的哈希函数值，而非存储原始密码

### MD5 与 SHA1的区别

长度、安全、速度、攻击

## 数字签名

> 数字签名是一种电子形式给一个消息签名的方法

签名：用**私钥**加密信息达到**身份验证**目的的运算

### 数字签名的目的

> 可验证性和不可伪造性

1. 提供一个手写签名的**数字化副本**

2. 数字签名保障了：

- 数据完整性
- 系统可用性
- 实体认证性
- 行为不可抵赖性

### 数字签名属性

1. 核实**作者签名和日期的时间**
2. 在签名时**对内容进行身份验证**
3. 为解决争端，能够被**第三方核查**

### 数字签名要求

1. 依赖于消息**位模式**
2. 使用特有的信息
3. 生成相对容易
4. 伪造在计算上不可行
5. 可以保留副本

### 数字签名方法

#### 普通数字签名

> **RSA ElGamal Schnorr** 椭圆曲线数字签名算法、**DSA数字签名算法**、有限自动机数字签名算法。

####  特殊数字签名

> 不可否认签名、门限签名、盲签名、代理签名

#### 功能：

1. 确认信息是由签名者发送（**实体认证性**）
2. 确认信息自签名到收到未被篡改（**数据完整性**）
3. 签名者无法否认签名由自己发送（**行为不可抵赖性**）

### 数字签名解决方案

#### 直接数字签名方案

> 前提：假设目标知道源的公钥 

- 只涉及通信的源点X和终点Y双方
- X的公钥$$K_{Ux}$$私钥$$K_{Rx}$$
- X可以使用私钥对消息报文的散列值进行加密来形成数字签名

##### 安全性

1. 安全性依赖于私钥的安全性
2. 但不具备行为不可抵赖性，即使每个报文中包含时间戳，仍可以被伪造。

#### 基于仲裁的数字签名方案

> 通过引入仲裁来解决直接签名方案中的问题，但仲裁者必须是一个受所有通信方信任的机构。

X->A->Y，A检验报文和签名的出处、内容，注明日期，加上仲裁证实标记。

#### 方案内容

1. **密钥生成**，如公钥密码体制
2. **签名算法**，利用私钥进行签名
3. **验证算法**，利用公钥验证签名

### RSA 数字签名

#### RSA 公钥算法

$$
大素数p、q \newline
n=p\times q\quad \phi(n)=(p-1)(q-1) \newline
随机正整数1<e<\phi(n),满足gcd(e,\phi(n))=1\newline
计算d 满足 d\times e =1(mod\quad\phi(n))\newline
加密变换： 对明文m\in Z_n, c=m^e (mod\quad n)\newline
解密变换： 对密文c\in Z_n, m=c^d (mod\quad n)
$$

> 手算RSA的前置技能：**模逆运算**（扩展欧几里得算法）**快速幂模运算**

#### 数字签名实现

密钥的生成完全相同，签名时：
$$
m\in Z_n, Sig(m)=m^d(mod \quad n)\newline
S\in Z_n, 若m=S^e (mod\quad n)，则认为s为消息m的有效签名
$$

### ElGamal 数字签名

#### 密钥参数生成

$$
选定大素数p\\\\
选取g,x<p\\\\
y=g^x(mod\quad p)\\\\
\{y,g,p\}即为公钥，x为私钥
$$

#### 签名过程

$$
K=公钥\{y,p,g\}私钥 x\\\\
签名算法：对随机数k,待签消息m，定义Sig(x,k)=(r,s)\\\\
r=g^k(mod\quad p),s=(m-xr)k^{-1}mod(p-1)\\\\
验证算法：Ver(m,r,s): y^rr^s\equiv g^m(mod\quad p)?true:false
$$

#### 注意

1. 每次签名应选择不同的随机数k，否则有可能私钥x能被算出
2. ElGamal 效率低于RSA 且有两倍的消息扩张
3. ElGamal数字签名也可以推广到合适的循环群中，某些变形能被证明在选择消息攻击下是安全的



## 练习

### RSA

![image-20220531222601334](/Cryptology.assets/%7D/image-20220531222601334.png)

### ElGamal

![image-20220531222634457](/Cryptology.assets/%7D/image-20220531222634457.png)

### Diffie-Hellman

![image-20220531222714991](/Cryptology.assets/%7D/image-20220531222714991.png)

### 有限域上的多项式乘法

![乘法](https://img-blog.csdnimg.cn/b6857bd9511343cb8ab5113d71c945cc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAYnVndW_opb_nk5w=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

### 有限域多项式求逆

![image-20220531223613620](/Cryptology.assets/%7D/image-20220531223613620.png)





[^有限域上的四则运算]: https://www.cnblogs.com/xdyixia/p/12576437.html

[^ruanx.net]: https://www.ruanx.net/

[^CTF-Wiki]: https://ctf-wiki.org/crypto/classical/introduction/
[^RSA共模攻击]: https://1ablades.github.io/2017/08/09/RSA%E5%85%B1%E6%A8%A1%E6%94%BB%E5%87%BB/

