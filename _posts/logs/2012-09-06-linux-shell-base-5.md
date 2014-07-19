---
layout: post
title: Linux shell 基础 5
date: 2012-09-06 10:06:20
categories:
 - logs
tags:
 - server
description: Shell脚本基础学习。最近在 LAMP 新手群，和几个朋友一起 YY 讲课，其中 Shell 部分由我负责，相关笔记整理了一下，也方便自己。
---

Linux shell 基础 5

#### 环境：
Linux 2.6.18-92.1.18.el5 \#1 i686 i686 i386 GNU/Linux
/bin/bash

#### 日志格式：
时间 [session] A1:时间戳 A2:时间戳 ……

#### 需求：
1）自定义区间划分数量；
2）自定义区间步长，即时间间隔；
3）统计出每个区间在自定义的时间间隔内出现的次数，求其分布率；
4）统计每次完整请求，即一个 session 内的损耗时间；（下例练习代码中未实现）
5）……

#### 日志文件：
路径：/data/applogs/20120831/{1..10}/monitor/xxxxx.log
{1..10}：指 1~10 台主机 IP
日志内容较多，现在只放两行给大家参考。

00:10:06.51948    [sushi9 9907]    [MAIN][LN:330]    A1:1343751006.375789    A2:1343751006.382490    A3:1343751006.383180    A4:1343751006.386621    A5:1343751006.386888    A7:1343751006.391899    A8:1343751006.392136    A9:1343751006.517964    A10:1343751006.518225    A11:1343751006.518905
00:17:54.14718    [qvpf1l 9904]    [MAIN][LN:330]    A1:1343751474.090370    A2:1343751474.100151    A3:1343751474.100627    A4:1343751474.103956    A5:1343751474.104257    A7:1343751474.111169    A8:1343751474.111417    A9:1343751474.145619    A10:1343751474.145886    A11:1343751474.146837

#### 简单实现代码：

{% highlight shell linenos %}
\#!/bin/bash

function analyzer () {
    \# * 代表当前目录所有内容
    for ff in *; do
        if [ -d "$ff" ]; then
            \# 递归查找
            cd $ff
            file
        elif [ -f "$ff" ]; then
            \# 分析日志
            do_analyzer "`pwd`/$ff"
        fi
    done
    cd ..
}


function do_analyzer () {
    file=$1
    \# 区间时长，单位毫秒
    step=1
    column=10
    FS="\t"
    awk -F "$FS" -v step="$step" -v column="$column" 'BEGIN {
        for(i = 0; i <= column; ++i) {
            if(i < column) {
                start = i * step
                end = (i + 1) * step
                format = start "-" end
            } else {
                end = i * step
                format = ">" end
            }
            printf "\t%s", format
        }
        printf RS
    }
    {
        for(i = 4; i < NF; ++i) {
            n = i + 1
            split($i, _cur, ":")
            split($n, _next, ":")
            block = _cur[1] "-" _next[1]
            response = (_next[2] - _cur[2]) * 1000
            pos = int(response/step)

            blockArray[block]
            if(i < column) {
                result[block FS pos]++
            } else {
                result[block FS column]++
            }
        }
    }
    END {
        for(b in blockArray) {
            printf b FS
            for(i = 0; i <= column; ++i) {
                printf result[b FS i]+0 FS
            }
            printf RS
        }
    }' "$file" | sort -t \- -k1.2n
}

date=$1
_date=$(date  +%Y%m%d)

\# 注意，别少了 -
date=${date:-$_date}
path="/data1/applogs/${date}/"
[ -d "$path" ] || exit 0


cd $path
analyzer
{% endhighlight %}

#### 程序结果：
        0-1     1-2     2-3     3-4     4-5     5-6     6-7     7-8     8-9     9-10    >10
A1-A2   0       0       0       0       0       8       36      1       0       19      38
A2-A3   138     0       0       0       0       0       0       0       0       0       0
A3-A4   0       0       1       43      3       1       76      4       4       2       0
A4-A5   138     0       0       0       0       0       0       0       0       0       0
A5-A6   0       0       0       0       2       1       0       0       0       0       0
A5-A7   0       0       0       0       93      23      3       0       1       2       1
A7-A8   135     0       0       0       0       0       0       0       0       0       0
A8-A9   0       0       0       0       0       0       0       0       0       0       135
A9-A10  0       0       0       0       0       0       0       0       0       0       134
A10-A11 0       0       0       0       0       0       0       0       0       0       134
A11-A26 0       0       0       0       0       0       0       0       0       0       4
A11-A32 0       0       0       0       0       0       0       0       0       0       7
A26-A27 0       0       0       0       0       0       0       0       0       0       4
A27-A28 0       0       0       0       0       0       0       0       0       0       4
A28-A29 0       0       0       0       0       0       0       0       0       0       4
A32-A33 0       0       0       0       0       0       0       0       0       0       7
A33-A34 0       0       0       0       0       0       0       0       0       0       7
A34-A35 0       0       0       0       0       0       0       0       0       0       7

结束语：
今天是最后一节课，基础的 linux shell 都已经讲解完了，或许有部分同学不是太明白，
但还是像我开始讲课时说的“shell 脚本其实就是 linux 命令 + 简单逻辑控制”，
只要大家掌握之前讲的基础命令和语言逻辑的部分内容，后面的简单案例相对来说都是可以理解的。

这些简单的案例或许不是很全面，主要是我想把讲的内容都串起来，是有针对性的，望理解。
另外，更多高级的案例，以后大家可以一起探讨。
谢谢
------------------------

@-健一
16:17 2012/9/2