---
layout: post
title: 关于 grep 的使用 
date: 2012-05-18 11:00:08
categories:
 - developer
tags:
 - server
description: 关于 Linux grep 命令的常用模式
---

文本查找有
grep egrep fgrep

正则表达式 ：
一般字符与特殊字符  基础正规表示法  延伸正规表示法

在这里就不累赘关于表达式的用法了，只想说一下 grep 的两个模式：

-E, --extended-regexp
Interpret PATTERN as an extended regular expression_r(see below).
相当于egrep命令，egrep命令只跟grep有很小的不同，egrep是grep的扩展，支持更多的re元字符。

-P, --perl-regexp
Interpret PATTERN as a Perl regular expression.
该模式即是以 Perl 正则扩展，其强大程度不是 -E 模式可以比拟的。

所以如果你发现使用 -E 模式处理某个文件无效，但 pattern 在理论上是没有错误的时候，
不妨试试 -P 模式，一定会让你看到意外且满意的结果。
