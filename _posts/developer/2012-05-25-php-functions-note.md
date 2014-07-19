---
layout: post
title: PHP函数笔记 
date: 2012-05-25 10:47:47
categories:
 - developer
tags:
 - php
description: 一些非常有用，但又不怎么用过的 PHP 内置函数，学习时顺带做一下笔记，方便日后查询使用。
---

#### 1、int strtotime ( string $time [, int $now ] )
将任何英文文本的日期时间描述解析为 Unix 时间戳

习惯用法：
1）$date = date('Y-m-d', strtotime('+10 days', strtotime('2012-05-25')));
2）$date = date('Y-m-d', strtotime('2012-05-25') + (24 * 60 * 60 *10));
优化用法：
1）$date = date('Y-m-d', strtotime('+10 days 2012-05-25'));
2）$date = date('Y-m-d', strtotime('2012-05-25 +10 days'));


#### 2、mixed highlight_file ( string $filename [, bool $return = false ] )
语法高亮一个文件。
例如：

{% highlight php linenos %}
<?php
$date = '2012-05-25';
echo date('Y-m-d', strtotime($date . ' +10 days'));
echo '<hr />';
highlight_file(__FILE__);
?>
{% endhighlight %}


#### 3、void register_shutdown_function ( callable $callback [, mixed $parameter [, mixed $... ]] )
把要调用的函数调入内存，当页面所有PHP语句都执行完成时，再调用此函数。

注意：
register_shutdown_function　是指在执行完所有PHP语句后再调用函数，不要理解成客户端关闭流浏览器页面时调用函数。因此，不能使用相对路径，因为PHP已经当原来的页面不存在了。就没有什么相对路径可言。
可以这样理解调用条件：
1）当页面被用户强制停止时
2）当程序代码运行超时时
3）当PHP代码执行完成时


#### 4、mixed set_error_handler ( callable $error_handler [, int $error_types = E_ALL | E_STRICT ] )
设置用户自定义的错误处理函数。

主要作用：
自定义错误处理函数，防止错误路径泄露。
当然，也可以把 php.ini - display_errors 设置为Off来解决。

例如：

{% highlight php linenos %}
<?php
// 摘录自手册
function myErrorHandler($errno, $errstr, $errfile, $errline)
{
    // find it in the PHP Manual
    // thx.
}
?>
{% endhighlight %}

#### 5、bool array_walk_recursive ( array &$input , callback $funcname [, mixed $userdata ] )
将用户自定义函数 funcname 应用到 array 数组中的每个单元。本函数会递归到更深层的数组中去。典型情况下 funcname 接受两个参数。input参数的值作为第一个，键名作为第二个。如果提供了可选参数 userdata，将被作为第三个参数传递给 callback funcname。

主要作用：
该函数为内置的递归函数，其效率远要高于自定义的递归函数。

例如：

{% highlight php linenos %}
<?php
//习惯用法：
function new_addslashes($string){
    if(is_array($string)) {
        foreach($string as $key => $val) $string[$key] = new_addslashes($val);
    } else {
        $string = addslashes($string);
    }
   
    return $string;
}

//优化用法：
function new_addslashes($string){
    if(is_array($string)) {
        array_walk_recursive($string, 'addslashes');
    } else {
        $string = addslashes($string);
    }
   
    return $string;
}
?>
{% endhighlight %}

同样地，也可以用在 stripslashes / htmlspecialchars 等递归的地方。

#### 6、array func_get_args ( void )
Gets an array of the function's argument list.

This function may be used in conjunction with func_get_arg() and func_num_args() to allow user-defined functions to accept variable-length argument lists.

#### 7、array array_map ( callback $callback , array $arr1 [, array $... ] )
array_map() 返回一个数组，该数组包含了 arr1 中的所有单元经过 callback 作用过之后的单元。callback接受的参数数目应该和传递给 array_map() 函数的数组数目一致。

#### 8、bool is_a ( object $object , string $class_name )
如果对象是该类或该类是此对象的父类则返回 TRUE，否则返回 FALSE。
注意：(PHP 4 >= 4.2.0, PHP 5)
is_a() 函数已废弃，自 PHP 5 起请使用 instanceof类型运算符。上例在 PHP 5 中会是这样：

{% highlight php linenos %}
<?php
if ($WF instanceof WidgetFactory) {
    echo 'Yes, $WF is a WidgetFactory';
}
?>
{% endhighlight %}

#### 9、void parse_str ( string $str [, array &$arr ] )
Parses str as if it were the query string passed via a URL and sets variables in the current scope.
The magic_quotes_gpc setting affects the output of this function, as parse_str() uses the same mechanism that PHP uses to populate the $_GET, $_POST, etc. variables.

#### 10、array parse_url ( string $url )
本函数解析一个 URL 并返回一个关联数组，包含在 URL 中出现的各种组成部分。

本函数不是用来验证给定 URL 的合法性的，只是将其分解为下面列出的部分。不完整的 URL 也被接受，parse_url() 会尝试尽量正确地将其解析。


结束语：
泡杯咖啡/茗茶，手捧PHP手册，也是一种不错的生活。

changed time: 10:04 2012/9/18
