---
layout: post
title: 好记性 不如烂笔头
date: 2012-06-20 19:29:26
categories:
 - logs
tags: ""
description: 好记性不如烂笔头，把时间当作朋友。我相信某天我会回来的，回来找寻她们！
---

#### 1、apache 日志
关键点：/full/path/httpd/var/logs/

配虚拟主机，不知道配置文件哪里写错了，apache一直无法启动，且没有任何提示信息，耗了一个下午的时间都未能解决；

老大过来指点一二，提示看看apache日志；
问题得到解决，完整的错误信息都已经记录下来；

#### 2、smarty 3.1.10 模板信息配置

{% highlight php linenos %}
<?php
$smarty = new Smarty();
$smarty->setTemplateDir('/full/path/template');
$smarty->setCompileDir('/full/path/compile');
$smarty->setCacheDir('/full/path/cache');
$smarty->setConfigDir('/full/path/config');
$smarty->left_delimiter = '{_';
$smarty->right_delimiter = '_}';

$smarty->display('template_name.tpl'); // (重点)
?>
{% endhighlight %}

在 display 时注意模板名称首尾是否有路径分隔符，若有请去除，
否则有时候会碰到意想不到的结果，会让你纠结好久的。

#### 3、mysql的group by的使用
默认情况下，select * from table group by field1后，取的是每一个分组的第一条数据，
此时若想取每一个分组的最后一条数据，可以对group by添加 desc 修饰符，即：
select * from table group by field1 desc，即可按倒序分组（或按某字段过滤相同的数据），
这样就可以只取每一个分组的最后一条了。

曾经居然用这样笨的方法：
select * from table id in (select max(id) from table group by field1)
后来因为效率问题，将该 sql 改为分两次查询，真够愚蠢的。

#### 4、mysql 表数据的替换
问题：table 存在 field1, field2两字段，现需要将 field1 更新为 field1 和 field2 的合并值，并将其中的 net 字符替换为 com 字符。
如下：
update table set field1=replace(concat(field1,field2),'net','com')

#### 5、访问页面显示空白
LAMP 环境，在没有任何前兆之下，访问突然都显示空白，代码也没任何变更，这个问题排查了一整天也没有找到原因，只知道 smarty 编译产生的文件都是空的，没有内容。后来，经过某人的帮忙，5 分钟就搞定了。
原因：磁盘空间满了，无法写入内容。
命令：df -lh

#### 6、MySQL 按某字段分组，每组取前 N 条
基本表结构
table
id   shopid
1    10001
2    10002
……
……
98  20001
99  20002
需求：按 shopid 前 3 位分组后，每个分组取 100 条数据。
select * from tb A where 100>=(select 1 from tb B where A.substring(shopid,1,3)=B.substring(shopid,1,3) and A.id

#### 7、按多个字符分割字符串

{% highlight php linenos %}
<?php
// 方法1
$string = 'one; two,three|     four  | five;six seven,';
$string = str_replace(array(';', ',', '|'), ' ', $string);
$string = preg_replace("/\s+/", ' ', $string);
$array = explode(' ', $string);
print_r($array);

// 方法2
$string = 'one; two,three|     four  | five;six seven,';
$array = preg_split("/[;,| ]+/", $string);
print_r($array);
?>
{% endhighlight %}

#### 8、sprintf 函数中保留 % 字符语义
如 $format = select ... from %s where field like '100%';
sprintf($format, $table);
这时候 sprintf 会把 100% 中的 % 也会当占位符解释，然后报错

正确使用：$format = select ... from %s where field like '100%%';

#### 9、正则检测数字是否合法
{% highlight php linenos %}
<?php
$parrent = "/^\d+(\.\d{2})?$/";    // 两位小数或者没有小数
$money = "100";
var_dump(preg_match($parrent, $money));
?>
{% endhighlight %}

#### 10、远程 rsync
1）rsync -a rsync://user_club@**.**.**.**:/data/etlfiles/club
2）rsync -a user_club@**.**.**.**::[目的端rsync模块名]

#### 11、mysql & inner join
update st_prop as a inner join st_player as b on a.attrid=b.attrid set a.uid=b.uid

- - -

last changed: 16:18 2014/2/21
持续更新
