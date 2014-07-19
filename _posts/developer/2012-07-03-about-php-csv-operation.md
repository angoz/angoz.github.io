---
layout: post
title: 关于CVS数据的操作
date: 2012-07-03 09:22:30
categories:
 - developer
tags:
 - php
description: 关于 CVS 数据，即把数据格式化为逗号分隔的值，以便导入到电子表格 Excel 或者数据库中（对于数据库也可以使用制表符\t）。
---

关于 CVS 数据，即把数据格式化为逗号分隔的值，以便导入到电子表格 Excel 或者数据库中（对于数据库也可以使用制表符\t）。

### 一、
对于 CSV 数据，在此文之前，本人一直就【逗号分隔】为关键点而走向了一个误区，即单纯地理解为将数据连接并以逗号为分隔，如下代码：

{% highlight php linenos %}
<?php
$data = array(
    array('a', 'b', 'c', 'd'),
    array('e', 'f', 'g', 'h'),
);

$handle = fopen('file.csv', 'w') or die('Can\'t open file.csv');
foreach($data as $row) {
    $content = implode(',', $row);
    fwrite($handle, $content . "\n");
}
fclose($handle);
?>
{% endhighlight %}

单纯上来，这确实也是“把数据格式化为逗号分隔的值”，无可厚非。而读取 CSV 数据，也就相对 implode 而使用 explode(',', $line); 来产生一个数据数组。

很明显地，对于 php 来说，做这样的事情是很容易的，但在数据量较大，且追求性能更极致的情况下，这肯定会是程序上非常不严谨，甚至是一个性能诟病，于是，便有了后话。

### 二、
在 php 手册上，很偶然地翻到文件处理一章并发现 fgetcsv 函数，相对地，则必有 fputcsv，再顺便看看 example之后，对于 CSV 数据的操作完全重新有了另一番的认识，

首先，在生成 CSV 文件上，使用 fputcsv() 函数由数据数组生成一行 CSV 格式的数据并写入到一个文件中，如下：

{% highlight php linenos %}
<?php
$data = array(
    array('netease', '1997-06-xx', '2012-07-03', 'dinglei'),
    array('tencent', '1998-11-xx', '2012-07-03', 'mahuateng'),
    array('sina', '1998-12-xx', '2012-07-03', 'wangzhidong'),
    array('sohu', '2000-07-12', '2012-07-03', 'zhangzhaoyang'),
);

$handle = fopen('file.csv', 'w') or die('Can\'t open file.csv');
foreach($data as $line) {
    if(false === fputcsv($handle, $line)) {
        die('Can\'t write CSV.');
    }
}
fclose($handle);
?>
{% endhighlight %}

### 三、
如果想输出 CSV 格式的数据而不是将其写入到一个文件中，可以使用特殊的输出流来代替文件，
即：php://output 代替 fopen 中的文件。如下：

// $handle = fopen('file.csv', 'w') or die('Can\'t open file.csv');
$handle = fopen('php://output', 'w');

其它不变，这样就可以直接向用户输出 CSV 格式化的数据了。

### 四、
再如果想把 CSV 格式的数据放到一个字符串中，而不是输出或者写入到文件，可以把输出缓冲配合输出流，如下：

ob_start();  // open
$handle = fopen('php://output', 'w');
`
$string = ob_get_contents();  // get
ob_end_clean(); // close

### 五、
如果想打开一个 CSV 数据文件，在 fopen 后，用 fetcsv() 函数读取其中的数据，如下：

{% highlight php linenos %}
<?php
$handle = fopen('file.csv', 'r') or die('Can\'t open file.');
$data = array();
while($line = fgetcsv($handle)) {
    // do something what you want
    // replace 'explode' with 'fgetcsv'
    $data[] = (array) $line;
}
fclose($handle);
?>
{% endhighlight %}

而根据 php 手册，我们知道 fgetcsv 第二个参数 length 是可选的，若设置值必须大于 CVS 文件内最长的一行。在 PHP 5 中该参数是可选的。如果忽略（在 PHP 5.0.4 以后的版本中设为 0）该参数的话，那么长度就没有限制，不过可能会影响执行效率。


最后，通过以上的一些基本说明，突然发现自己以前是多么的愚蠢，然后放着 fputcsv 和 fgetcsv 不用，而是用 implode 和 explode。另外，根据手册上的说明得知，以后不要再试图绕过 fputcsv 和 fgetcsv 函数，因为 CSV 的实际情况要比 im... 和 ex... 这种方式能够处理的格式更复杂，比如说某些字段值中包含逗号直接量时，不应该将字段中的逗号当成是数据的分隔符，很显然的，implode 会把逗号当这样处理，但是使用 fgetcsv() 函数可以保证我们的代码避免这些不明显的错误。
