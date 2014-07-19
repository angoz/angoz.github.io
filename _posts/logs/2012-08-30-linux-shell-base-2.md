---
layout: post
title: Linux shell 基础 2
date: 2012-08-30 23:58:48
categories:
 - logs
tags:
 - server
description: Shell脚本基础学习。最近在 LAMP 新手群，和几个朋友一起 YY 讲课，其中 Shell 部分由我负责，相关笔记整理了一下，也方便自己。
---

linux shell 基础 2


#### [历史操作]
history    查看所有使用过的命令记录
history | grep cd    只查看包含 cd 字符的命令（一般也指只查看 cd 命令）
!cd    执行最后一次以 cd  开头的命令
!!    执行最后一次使用的命令

who    查看系统当前在线用户
last    查看所有系统登录/退出记录


#### [用户管理]
adduser user    添加用户
passwd user    修改用户密码

groupadd group    添加用户组
useradd -g group user    新建用户同时增加用户组
usermod -G group user    给用户增加用户组
pgasswd -a user group    向用户组增加用户

passwd user -l    锁定用户
passwd user -u    释放用户

usermod -G group user    删除用户（连同家目录也被删除）
gpasswd -d user group    从组中删除用户


#### [补充]
ls -lS    按文件大小排序 -- 降序
ls -lS | head -2    查找最大的 2 个文件

chgrp group file.txt    改变 file.txt 所属用户组
chown group file.txt    改变 file.txt 所属用户
chmod 755 file.txt    改变 file.txt 权限


#### [文件比较]
cmp file1.txt file2.txt    
比较两个文件，如果文件相同，则不显示消息。如果文件不同，则显示第一个不同的位置。
如果显示消息 cmp: EOF on file1.txt，则 file1.txt 的第一部分与 file2.txt 相同，但在 file2.txt 中还有其他数据。

diff file1.txt file2.txt    显示文件 file1.txt 和 file2.txt 的不同之处

#### [格式、编码转换]
dos2unix file.txt    转换文件为标准的 unix 文件格式
unix2dos file.txt    转换文件为标准的 windows 文件格式

iconv    转换内容编码
iconv -f 原编码 -t 新编码 old.txt > new.txt

批量转换编码：
find /src_path -type d -exec mkdir -p /target_path/utf8/
在 utf8/ 下创建一个与源目录 src_path 路径相同的目录

find /src_path -name "*.php" -exec iconv -f old_charset -t new_charset {} -o
/target_path/utf8/    将所有 php 文件从 old_charset 转换成 new_charset，并输出到 /target_path/utf8/ 目录下。


#### [内容操作]
uniq file.txt    以行为单位，去掉重复的行
uniq -c file.txt    统计重复次数

wc    统计内容
wc file.txt    返回结果为 4 列，分别为：行数，单词数，字节数，文件名
wc -l file.txt    统计行数
wc -w file.txt    统计单词数
wc -m file.txt    统计字节数

sort    排序
sort file.txt        升序、基数排序，即：1,11,2,23,24（靠左，逐位比较）
sort -n file.txt     升序、自然排序，即：1,2,11,23,24
sort -r file.txt    降序、基数排序
sort -rn file.txt    降序、自然排序

rev    以行为单位，反转内容输出
rev file.txt    反转输出，如：123456789 反转后为 987654321

sed    一个非交互式编辑器（Sed  is  a  stream editor. @_@ 其实我也不知道叫什么玩意）
sed 's/原内容/新内容/' file.txt    将 原内容 替换为 新内容（以仅为单位，仅替换一次）
sed 's/原内容/新内容/g' file.txt    全局替换

awk    一个文本处理工具
awk '{.....}' file.txt    在 { } 中自定义对 file.txt 的处理方式
echo "a b c d e f g" | awk '{print $3}'    awk 默认以空格分割字符串，并输出分割后的第三个位置的内容
echo "a|b|c|d|e|f|g" | awk -F "|" '{print $3}' awk 以 | 分割字符串，并输出分割后的第三个位置的内容

（sed 和 awk 是非常强大的文本工具，可阅读 《sed&awk》 一书）


#### [程序操作]
df -lh    查看盘符大小
df -T    查看盘符大小以及盘符类型

fdisk    盘符操作，包括创建、删除等（man disk 可查看详细内容）

ps -aux    查看某个时间点的程序运作情况
ps -aux | grep httpd    只查看包含 httpd 字符的进程（一般也只有 httpd 进程）

kill pid    结束 pid 程序（pid 为进程 id，可通过  ps -aux 查看）
kill -1 pid    重启 pid 程序
kill -9 pid    强制结束 pid 程序

top    系统性能，进程的资源占用状况监控（类似 windows 的任务管理器）
top -d second    以 second 秒为间隔刷新监控信息
top -p pid    只监控 pid 程序的信息

service    服务管理（三种操作：start/stop/restart）
service network start    启动网络服务（赞同于/etc/init.d/network restart）

#### [附加内容]
tar    打包/解包命令
tar -zxvf file.tar.gz    对 file.tar.gz 解包到当前目录
tar -zxvf file.tar.gz -C /path    对 file.tar.gz 解包到 /path 目录下
tar -zcvf file.tar.gz /path/*    对 /path 下的所有内容打包成 file.tar.gz

-c    创建
-v    输出处理过程
其它参数参考：man tar


在输入命令时，写了很长的一行，这时候不想用这个命令了，该怎么清除？
1）用 del 键，一个一个删除；
2）ctrl + u 清空光标左侧所有内容，光标在末尾则为清除整行命令。**


#### [vi/vim操作]
说明：n 为偏移量 offset

j    下一行
nj    下移 n 行
k    上一行
nk    上移 n 行
h    左移
nh    左移 n 个字符
l    右移
nl    右移 n 个字符

gg    文件第一行
ngg    文件 n 行
G    文件最后一行
H    屏幕第一行
M    屏幕中间一行
L    屏幕最后一行

0    行首
$    行尾
{    段首
}    段尾
x    删除 1 个字符
u    后退（比如：dd 删除一行，u 后退则为恢复）
ctrl + r    前进（与 u 相反）
nx    删除 n 个字符
d$    从光标处删除到行尾
d0    从行首删除到光标前一个字符
r    替换 1 个字符
nr    替换 n 个字符
R    连续替换，esc 退出替换状态

dd    删除一行
ndd    删除 n 行
yy    复制一行
nyy    复制 n 行
p    粘贴

:w    保存
:q    退出
:q!    不保存强制退出
:wq    保存退出
:x    保存退出
:set nu    显示行号
:set nu!隐藏行号

:%s/查找内容/退换内容/    替换一次
:%s/查找内容/退换内容/g    全局替换

/查找内容    查找内容（附：n 是下一个，N 是上一个）

以上的 vi/vim 操作，更多内容请另外学习，不做赘述。
其中，偏移量 n 可以在很多命令中结合使用。


17:10 2012/8/24