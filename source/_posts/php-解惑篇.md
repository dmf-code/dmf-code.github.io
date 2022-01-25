---
title: php-解惑篇
date: 2021-06-24 21:26:41
tags: 问题集
categories: php
cover: https://cdn.jsdelivr.net/gh/dmf-code/picture@main/picGo/20210624213207.png
---

### php 的 `ltrim` 函数为什么会乱码？

#### 场景

在 `laravel` 论坛中发现有人提出了这个问题 `ltrim('你好世界', '你好')`，结果返回了乱码数据，然后不知道是怎么产生乱码的。

#### 问题刨析

`php` 在官网教程中明显说明涉及中文的操作都要使用 `mb_` 前缀函数，这里其实是多字节编码导致的。 `php` 默认处理的是 `ascii` 码，然后中文的话默认都是多字节，所以使用 `ltrim` 时就会出现乱码问题。

```php
<?php

// 字符串转ascii码
function str_to_ascii($str): string
{
    $str=mb_convert_encoding($str,'utf-8');
    $change_after='';
    for($i=0, $iMax = strlen($str); $i< $iMax; $i++){
        $temp_str=dechex(ord($str[$i]));
        $change_after.=$temp_str[1].$temp_str[0];
    }
    return strtoupper($change_after);
}


$str = '你好世界';
$rStr = '你好';


$newStr = ltrim($str, $rStr);


var_dump(str_to_ascii($str));
var_dump(str_to_ascii($rStr));
var_dump($newStr);
```

打印结果：

```text
string(24) "4EDB0A5E5ADB4E8B697E59C8"
string(12) "4EDB0A5E5ADB"
string(5) "¸界"
```

上面的结果是 16 进制显示的结果，拆分出来就是`你：4EDB0A 好：5E5ADB 世：4E8B69 界：7E59C8`,然后一个字节 8 比特，所以你字由 `4E DB 0A` 三个字节构成了。所以 `ltrim` 要过滤的数组就变成了
`[4E, DB, 0A, 5E, 5A, DB]`。

#### 总结

查看过滤数组发现，世字第一个字节会被抹去，然后才会找到非过滤元素，`ltrim` 函数才能结束。
