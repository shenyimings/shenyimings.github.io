---
author: "Yiming Shen"
date: 2022-04-02
lastmod: 2022-04-02
title: "PHP Tutorial"
description: "PHP is the best language for web programming."
tags: [
    "PHP",
    "Web",
    "Note",
]
categories: [
    "Notes", 
    "PHP",
]
math: true
ShowToc: true
draft: false
typora-root-url: ..\..\..\static\
---

## 正则表达式

使用`PCRE`库函数`preg_match`

[^PHP: preg_match - Manual](https://www.php.net/manual/zh/function.preg-match.php)

**`pattern`**
要搜索的模式，字符串类型。

**`subject`**
输入字符串。

**`matches`**
如果提供了参数matches，它将被填充为搜索结果。 \$matches[0]将包含完整模式匹配到的文本， \$matches[1] 将包含第一个捕获子组匹配到的文本，以此类推。

**`flags`**

可以被设置为以下标记值的组合:

- `PREG_OFFSET_CAPTURE`

  对于每一个出现的匹配返回时会附加字符串偏移量(相对于目标字符串的字节数)

- `offset`

  指定从目标字符串的某个位置开始搜索（单位是字节）

**返回值：**匹配次数

### 分隔符

```php
/ # ~
    如果模式中包含分隔符，需要使用反斜杠\进行转义
```

分隔符后可使用**模式修饰符**`i m s x`

### 模式修饰符

[^RegexLearn](https://regexlearn.com/zh-cn/cheatsheet)

*i* (`PCRE_CASELESS`)

如果设置了这个修饰符，模式中的字母会进行大小写不敏感匹配。

*m* (`PCRE_MULTILINE`)

默认情况下，PCRE 认为目标字符串是由单行字符组成的(然而实际上它可能会包含多行)， "行首"元字符 (^) 仅匹配字符串的开始位置， 而"行末"元字符 ($) 仅匹配字符串末尾， 或者最后的换行符(除非设置了 *D* 修饰符)

*s* (`PCRE_DOTALL`)

如果设置了这个修饰符，模式中的点号元字符匹配所有字符，包含换行符。如果没有这个 修饰符，点号不匹配换行符。这个修饰符等同于 perl 中的/s修饰符。 一个取反字符类比如 [^a] 总是匹配换行符，而不依赖于这个修饰符的设置。

*x* (`PCRE_EXTENDED`)

如果设置了这个修饰符，模式中的没有经过转义的或不在字符类中的空白数据字符总会被忽略， 并且位于一个未转义的字符类外部的#字符和下一个换行符之间的字符也被忽略。

*A* (`PCRE_ANCHORED`)

如果设置了这个修饰符，模式被强制为"锚定"模式，也就是说约束匹配使其仅从 目标字符串的开始位置搜索。这个效果同样可以使用适当的模式构造出来，并且 这也是 perl 种实现这种模式的唯一途径。

*D* (`PCRE_DOLLAR_ENDONLY`)

如果这个修饰符被设置，模式中的元字符美元符号仅仅匹配目标字符串的末尾。如果这个修饰符 没有设置，当字符串以一个换行符结尾时， 美元符号还会匹配该换行符(但不会匹配之前的任何换行符)。 如果设置了修饰符*m*，这个修饰符被忽略. 在 perl 中没有与此修饰符等同的修饰符。

*S*

当一个模式需要多次使用的时候，为了得到匹配速度的提升，值得花费一些时间 对其进行一些额外的分析。如果设置了这个修饰符，这个额外的分析就会执行。当前， 这种对一个模式的分析仅仅适用于非锚定模式的匹配(即没有单独的固定开始字符)。

### 练习

[^测试Regex](https://regexlearn.com/zh-cn/playground)

**匹配**

```php
<?php
$subject = "my email is spark@imooc.com";
//在这里补充代码，实现正则匹配，并输出邮箱地址
$pattern = "/(\w+@\w+.com)/";
preg_match($pattern,$subject,$matches);
print_r($matches[0]);
?>
```

```php
<?php
$str = "<ul>
            <li>item 1</li>
            <li>item 2</li>
        </ul>";
//在这里补充代码，实现正则匹配所有li中的数据
$pattern = "/<li>(.*)<\/li>/i";
preg_match_all($pattern,$str,$matches);
print_r($matches[1]);
```

**替换**

```php
<?php
$str = '主要有以下几个文件：index.php, style.css, common.js';
//将目标字符串$str中的文件名替换后增加em标签

$p = "/(\w+\.\w+)/";
$rep = '<em>$0</em>';
$str = preg_replace($p,$rep,$str);
echo $str;
?>
```

### 其他

```php
[^abc] //是否定字符集
^ //是字符串或行的开头
```

