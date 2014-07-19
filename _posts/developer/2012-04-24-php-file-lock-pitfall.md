---
layout: post
title: PHP文件锁定读写的一点注意
date: 2012-04-24 18:53:19
categories:
 - developer
tags:
 - php
description: 都说文本方式容易出现文件锁定失效等乱七八糟的问题。其实并不是失效, 而是写法有些不对, 尤其是在 win32之类的平台下运行时更要注意。被 lock_ex 后的文件 再以read模式 fopen 的话将读到空内容……
---

都说文本方式容易出现文件锁定失效等乱七八糟的问题。
其实并不是失效，而是写法有些不对，尤其是在 win32之类的平台下运行时更要注意。
被 lock_ex 后的文件 再以read模式 fopen 的话将读到空内容……
如果没有判断就把它当作空做操作后再写入就出错啦，很多问题出在这里。

再来补充一下：
如果一个文件被以write的模式fopen后并 flock(..LOCK_EX)的话
有另一个程序这时去 fopen 它,并且也要 flock(LOCK_EX) 的话，这肯定出错。
因为第二个fopen时文件的size可能已经被 truncate 成 0 了..

#### 下面做一个测试
- - -

代码一 ::: 1.php
{% highlight php linenos %}
<?php
$fd = fopen("./foobar.txt", "w");
flock($fd, LOCK_EX);
sleep(10);
fwrite($fd, "angoz.github.io");
flock($fd, LOCK_UN);
fclose($fd);
?>
{% endhighlight %}

代码二 ::: 2.php
{% highlight php linenos %}
<?php
$fd = fopen("./foobar.txt", "r");
echo fread($fd, 512);
fclose($fd);
?>
{% endhighlight %}

代码三  ::: 3.php
{% highlight php linenos %}
<?php
if ($fd = @fopen("./foobar.txt", "r+")) {
    flock($fd, LOCK_EX);
    fseek($fd, 0, SEEK_END);
    fwrite($fd, "hello world!");
    flock($fd, LOCK_UN);
    fclose($fd);
}
?>
{% endhighlight %}

形如测试程序（两种情况）：
1. 运行 1.php，然后马上去运行 2.php，2.php 的输出结果是空；
2. 运行 1.php，然后马上去运行 3.php，3.php 将在 1.php 运行结束后才能结束；不过结果文件内容是: "hello world!" 而不是 "angoz.github.io"。

所以特别要注意 write 时的情况，"w", "r+", "a", "a+" 都要注意文件长度为0的情况！可能文件是空，也可能是被 flock。
