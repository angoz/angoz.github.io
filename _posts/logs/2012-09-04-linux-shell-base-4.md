---
layout: post
title: Linux shell 基础 4
date: 2012-09-04 15:30:30
categories:
 - logs
tags:
 - server
description: Shell脚本基础学习。最近在 LAMP 新手群，和几个朋友一起 YY 讲课，其中 Shell 部分由我负责，相关笔记整理了一下，也方便自己。
---

Linux shell 基础 4

前几天已经讲解了大部分的 shell 常用命令，以及基本的 shell 变量声明、结构、循环、判断等基础，今天我们将直接练习一些简单的需求。

Q1：
{% highlight shell linenos %}
\#!/bin/bash

:<<HEREDOC
模仿 tree 命令，
生成树状结构的目录树。
HEREDOC

\# 修饰符
modify="|-- "

path=$1
\# 如果 path 为空则使用默认值 $PWD
path=${path:-$PWD}

\# 短路求值，若 path 不为目录，则退出程序
[ -d "$path" ] || exit 0

cd "$path"
modify="|-- "
function tree () {
    # 获取当前目录所有内容，包含隐藏内容
    # 并将内容保存在 $@ 中
    set $(ls -a)
   
    # 将 $@ 中头两个内容删除（分别为：. 和 .. ）
    shift 2

    # ${tab//-/ } 将 $tab 变量中的所有 - 替换为空格
    tab="${tab//-/ }$modify"
    for file in "$@"; do
        echo -e $tab$file
        # 如果是目录，则递归深入
        if [ -d "$file" ]; then
            cd $file
            tree
        fi 
    done
   
    # 从右往左截取第一个 | 之后的所有内容
    tab="${tab%|*}"
    cd ..
}

tree
{% endhighlight %}

当然我们也有这样的一个命令 tree，直接生成树状目录结构。（需求 yum 安装）

Q2:

\#!/bin/bash
{% highlight shell linenos %}
\# 需求说明，以下为 heredc
:<<HEREDOC
3 5 6 7
2 3 1 0
4 5 6 9
2 3 4 4
2 2 1 0
4 5 0 9

将以上内容放入 test.log 中

需求：
把每行第 2 列和第 3 列的和作为第 5 列，并求出第 5 列的值大于其所有行数的平均值的行数
HEREDOC

file="test.log"
[ -e "$file" ] || exit 0

\# 计算第五列
cat "$file" | awk '{val=$2+$3; print $0 " " val}' > test.tmp

\# 计算行数
lineCount=$(wc -l "test.tmp" | awk '{print $1}')

\# 计算平均值
arg=$(cat "test.tmp" | awk -v lc="$lineCount" '
BEGIN{ num = 0; total = 0; }
{ total = total + $NF }
END { arg = total / lc; print arg; }')


echo -e $lineCount
echo -e $arg

\# 求大于平均值的行数
cat "test.tmp" | awk -v arg="$arg" '{
    if($NF > arg) num = num + 1
}
END {
    print num
}'

rm -rf test.tmp


echo -e "-----------------------------"

\# 合并所有分步骤
cat "$file" | awk '{val=$2+$3; arr[NR]=val; total += val}
END {
arg = total/NR
for(i in arr) {
    if(arr[i] > arg) num++;
}
print num
}'
{% endhighlight %}


Q3:

{% highlight shell linenos %}
\#!/bin/bash

\# 需求说明，以下为 heredc
:<<HEREDOC
127.0.0.1 - - [14/Feb/2012:00:02:32 +0800] "GET /port/user.php?cla=baidu&key=e5d38743014f88f953269399282c86f5 HTTP/1.1" 200 4 "-" "-" 35622
127.0.0.1 - - [14/Feb/2012:00:02:32 +0800] "GET /test.php HTTP/1.1" 200 4 "-" "-" 35622
127.0.0.1 - - [14/Feb/2012:00:02:32 +0800] "GET /port/user.php?cla=google&key=e5d38743014f88f953269399282c86f5 HTTP/1.1" 200 4 "-" "-" 35622
127.0.0.1 - - [14/Feb/2012:00:02:32 +0800] "GET /port/port.php?cla=google&key=e5d38743014f88f953269399282c86f5 HTTP/1.1" 200 4 "-" "-" 35622
127.0.0.1 - - [14/Feb/2012:00:02:32 +0800] "GET /test.php HTTP/1.1" 200 4 "-" "-" 35622

将以上日志放入 apache.log 中

需求：
1）分析出 port 目录下各个 API 接口的访问日志
    格式：IP 日期 响应时间
    注：响应时间为日志最后的数值
2）计算各个 API 的平均响应时间，以及大于 500ms 的访问次数（讲课中没有说，可以自己完成，其实就是练习2）
HEREDOC


file="apache.log"
[ -f "$file" ] || exit 0

grep '/port/'  "$file" | awk -F "[ |[]" '{split($8, arr, "[/|.|?]");filename=arr[3] ".log"; printf("%s %s %s %s\n",$1,$5,$NF,arr[3]) > filename}'
{% endhighlight %}

Q4 附加：
需求：去重。

{% highlight shell linenos %}
\#!/bin/bash

echo 122333aacffff6666 | fold -1 | uniq | xargs | sed 's/ //g'
echo 122333aacffff6666 | fold -1|awk -F "" '!a[$0]++{printf $0}'
echo 122333aacffff6666 | sed ':a;s/\(.\)\(.*\)\1/\1\2/;t a'
echo 122333aacffff6666 | tr -s [:alnum:]
{% endhighlight %}


15:34 2012/8/30