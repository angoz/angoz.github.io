---
layout: post
title: Linux shell 基础 1
date: 2012-08-29 18:48:07
categories:
 - logs
tags:
 - server
description: Shell脚本基础学习。最近在 LAMP 新手群，和几个朋友一起 YY 讲课，其中 Shell 部分由我负责，相关笔记整理了一下，也方便自己。
---

Linux shell 基础 1

#### [基本符号]
/    系统根目录（类似 windows 我的电脑）
~    用户家目录（类似 windows 我的文档）
.    当前目录
..    上一级目录
*    所有内容（通配符）

#### [帮助手册]
命令手册命令：man  （manual）
man command    获取 command 命令手册信息，如：man ls
command --help    也可以获取相关的 command 帮助信息


#### [路径变更]
cd    改变目录 change directory（默认目录为家目录）
cd ~    转到家目录
cd ..    转到上一级目录
cd -    当前目录与前一次所在目录相互转换
pwd    查看所在位置的完整路径 print working directory


#### [目录内容查看]
ls    查看当前目录内容
ls /path    指定 path 查看其目录内容（类似 windows 查看方式-大图标）
ls -al /path    查看当前目录内容的详细信息（类似 windows 查看方式-详细列表）
ls -r /path    递归查看（以行方式直接显示）
ls -R /path     分级递归查看

tree /path    以树的形式查看 path 目录下的所有内容


#### [权限]
当 ls -al /path 查看时，显示内容的详细信息，如：
drwxr-xr-x 3 root     root     4096 08-21 21:18 dir2
列信息分别为：权限 子目录个数    所属用户组    所属用户    大小    最后更新时间    名称
drwxr-xr-x：第一位 d 表示：目录 direcoty，后面的每三位分别对应 所属用户组、所属用户、其他用户 对该内容的权限
r == 4 == 2^2    read 读
w == 2 == 2^1    write 写
x == 1 == 2^1    execute 执行

常见权限有：777、766、755
7 = 1 + 2 + 4 = rwx
6 = 2 + 4 = rw-
5 = 1 + 4 = r-x
其它权限类推

chown    改变内容所属用户
chown root file    将 file 文件的所属用户改为 root
chown root:root file    将 file 文件的所属用户组改为 root，所属用户改为 root
chown root:root -R /path    将 path 目录下的所有内容进行所属变更

chmod    改变内容的权限
chmod 755 file 将 file 文件的权限改为 rwxr-xr-x
chmod 755 -R /path    将 path 目录下的所有内容进行权限变更


#### [创建文件夹]
mkdir    创建文件夹
mkdir ~/workspace    在 ~ 下创建文件夹 workspace 文件夹
mkdir ~/workspace/{dir1,dir2,dir3}    创建多个同级目录
mkdir -p ~/workspace/dir1/dir2/dir3    创建多级目录
mkdir -p ~/workspace/dir1/{dir2,dir3}/dir4    结合

#### [创建文件]
touch    创建文件
touch file.txt    创建 file.txt 文件
（若 file.txt 文件已经存在，touch 则会更新 file.txt 文件的最后更新时间）
touch {11,22,33}.txt    创建多个同后缀文件
touch {1..9}.txt    创建 1.txt 至 9.txt 系列文件

#### [删除]
rm    删除
rm file.txt    删除 file.txt 文件
rm -r dir    删除目录
rm -rf dir    强制删除目录

#### [其它文件操作]
mv    重命名/剪切
mv file1 file2    将 file1 重命名为 file2
mv /path1/file /path2/file    将 path1 下的 file 剪切到 path2 下

cp    复制
cp file1 file2    将 file1 复制为 file2
cp /path1/file /path2/file    将 path1 下的 file 复制到 path2 下

ln    创建快捷方式
ln -s file.txt    link_file.txt    对 file.txt 创建 link_file.txt 快捷方式

#### [查看]
cat file.txt    查看 file.txt 文件，正向输出到屏幕
more file.txt    按屏幕大小分页，从头到尾查看，空格翻页
less file.txt    按屏幕大小分页，从尾到头查看，空格翻页
head file.txt    文件头查看，默认头部 10 行
head -5 file.txt    查看文件头 5 行
tail file.txt    文件尾查看，默认尾部 10 行
tail -5 file.txt    查看文件尾 5 行
tail -f file.txt    实时查看，类似监控 -f follow，日志文件内容变更监控常用
tac file.txt    查看 file.txt 文件，反向输出到屏幕

#### [查找]
find    查看当前目录内容，并显示内容路径，默认为当前目录，即 .
find /path    指定 path 查看其目录内容
find /path -type d    只查找 path 目录下的文件夹
find /path -type f    只查找 path 目录下的文件
find /path -name 'string'    只查找 path 目录下包含 string 字符的内容

grep    匹配查找
grep -R 'heixiake' .    匹配查找当前包含 heixiake 字符的文件
grep 'heixiake' *    匹配查找当前包含 heixiake 字符的文件
grep -P 'heixiake' *    以正则 POSIX 匹配查找当前包含 heixiake 字符的文件


#### [命令组合]
|    代表管道
command1 | command2    command1 执行后将结果通过管道输出给 command2 处理，最终得出合适的结果
如：cat file.txt | head -10 | grep 'heixiake'
获取 file.txt 内容，并用 head 过滤头部 10 行，再在 10 行中过滤只包含 heixiake 字符的内容


#### [定向输出]
cat file.txt > ~/log.txt cat file.txt的结果放到家目录下log.txt
cat file.txt > /dev/null 抛掉无用的，不让内容在屏幕显示


#### [其它]
date    输出当前日期
uname -a    查看系统版本
sed    待续
awk    待续


17:38 2012/8/22