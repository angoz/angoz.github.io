---
layout: post
title: Linux shell 基础 2
date: 2012-08-31 22:43:01
categories:
 - logs
tags:
 - server
description: Shell脚本基础学习。最近在 LAMP 新手群，和几个朋友一起 YY 讲课，其中 Shell 部分由我负责，相关笔记整理了一下，也方便自己。
---

linux shell 基础 3


\#    shell 注释符号，类似： //

=代码块注释：
while false; do
要注释的代码区间
done


echo $SHELL    查看 shell 版本
echo $PATH    查看环境变量内容

touch file.sh    创建 shell 脚本文件

脚本执行：
/bin/bash file.sh    方式 1（根据 echo $SHELL 决定）
/bin/sh file.sh        方式 2
sh file.sh
./file.sh

vi file.sh

\#!/bin/bash    第一行声明内容（根据 echo $SHELL 内容）
echo 'hello, world!'    # 不换行输出
printf 'hello, world!'    # 换行输出
echo -e 'hello, world!'    # 换行输出


\# shell 变量基本都可以理解为字符类型
\# 变量声明字母、数字、下划线，不能以数据开头
\# 变量赋值，赋值号两边不允许出现空格
var1=hello        # 不包含特殊字符的赋值（个人不推荐）
var2='hello, world!'    # 包含特殊字符的赋值（或者使用""）
var3="hey girls! $var2"    # 字符串连接（个人不推荐）
var3="hey girls! ${var2}"    # 字符串连接
echo $var3        # 输出变量

#### [字符串截取]
由于内容较多，大家参考：
http://tech.foolpig.com/2008/07/09/linux-shell-char/


num1=99
num2=23
num3=$(($num1+$num2))    # 算术运算
num3=$[$num1+$num2]    # 算术运算（个人不推荐，易与数组混淆）

str1=`date +%F`        # 得到 date +%F 命令返回的内容
str2=$(date +%F)    # 得到 date +%F 命令返回的内容
timestamp=$(date +%s)    # 得到 unix 时间戳


read -p "Pleases input your name:" name    # 输出提示让交互用户输入内容，并赋值给 name 变量
echo $name


#### [数组]
arr1=(value1 value2 value3)    # 以空格间隔
arr2=("value1" "value2" "value3")
arr3=([0]=value1 [1]=value2)

echo $arr1    # 直接输出数组名称，将得到第一个值
echo $arr1[0]    # 错误，仅输出第一值我[0]字符
echo ${arr1[0]}    # 输出第一个元素
echo ${arr1[@]}    # 输出所有元素
echo ${#arr1}    # 输出数组长度


unset arr1    # 删除 arr1 数组
unset arr1 arr2
unset arr1[0] arr2[0]


#### [if-elif-else 判断]
-lt    小于
-gt    大于
-eq    等于
-le    小于等于
-ge    大于等于
-o    或 ||
-a    与 &&

num=12
if [ "$num" -lt "10" ]; then    #  if、括号两侧必须带空格
    echo '<'
elif [ "$num" -eq "12" ]; then
    echo '='
else
    echo '>'
fi

if [ "$num" -lt "10" -a "$num" -eq "10" ]; then
\# 或者 if [ "$num" -lt "10" ] && [ "$num" -eq "10" ]; then
    echo '<='
else
    echo '>'
fi


#### [case-esac 多支选择]
\# 类似 PHP 的 switch 结构
var="start"
case "$var" in
    "start" "restart")
        echo 'do start or restart'
        ;;
    "stop")
        echo 'do stop'
        ;;
esac

#### [循环]
while false; do    # 简单结构
....
done

num=10
while [ $num -gt 0 ]; do
    \# do something...
    num=$(($num-1))    # shell 没有自增、自减（即$i++、$i--）语法
done


until [ $num -le 0 ]; do
    \# 同上
done


echo {1..9}    # 生成 1 至 9 的有序数列，以空格间隔
seq 1 9        # 生成 1 至 9 的有序数列，默认步长为 1，回车间隔
seq 1 2 9    # 生成步长为 2 的数列，即 1 至 9的奇数
seq -s " " 1 9    # 生成 1 至 9 的有序数列，步长为 1，空格间隔

\# 循环 9 次
for i in {1..9}; do
    echo -e $i
done

for i in `seq 1 9`; do
    echo -e $i
done



#### [函数]
function funcName
{
    \# do something...
}
或者
\# 个人不推荐
funcName
{
    \# do something...
}

$@    代表所有参数的数组，如 php 的 $argv
$#    代表参数的个数，如 php 的 $argc
$*    同 $@，只是数组表现形式不一样
$1、$2    依次代表参数1、参数2....
$?    代表函数最后返回的结果

如：
function foo
{
    echo -e $@
    if [ $# -lt 1 ]; then
        echo '没有参数';
        exit 0
    else
        echo $#
        echo -e " 个参数"
        echo -e $1
        exit 1
    fi
}

foo "param1" "param2"    # 调用 foo 函数，并传递两个参数
echo $?    # 获取函数最后返回的结果值


15:41 2012/8/27