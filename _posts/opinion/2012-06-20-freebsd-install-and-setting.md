---
layout: post
title: FreeBSD Install and Setting
date: 2012-06-20 10:13:37
categories:
 - developer 
tags:
 - server
description: 很喜欢 FreeBSD 这样的系统，定制性非常高，各种系统模块均出自自己，各重要的是 xfcg 桌面很帅气，非常 Geek 哦！
---

## 一、Install
Skipping...

## 二、Setting

#### 1、修改路由。。。不明白为何默认没有route
vi /etc/rc.conf
添加一行
defaultrouter="网关ip"

#### 2、修改root不能ssh
vi /etc/ssh/sshd_config
第十三行改为
PermitRootLogin yes
第六十六行改为
PasswordAuthentication no

#### 3、修改ports树
vi /etc/portsnap.conf
第十三行的SERVERNAME=默认的改为
SERVERNAME=portsnap3.hshh.org
如果是电信的话写成
portsnap4.hshh.org
portsnap2.hshh.org
然后执行
portsnap fetch extract
portsnap fetch update

#### 4、修改系统文件更新源，就是给系统打补丁
vi /etc/freebsd-update.conf
修改第十二行为
ServerName freebsd-updates.mirrors.163.com

#### 5、修改软件安装源
vi /etc/make.conf
内容如下
MASTER_SITE_BACKUP?=ftp://ftp.freebsdchina.org/pub/FreeBSD/ports/distfiles/${DIST_SUBDIR}/\
ftp://ftp.freebsd.org.cn/pub/FreeBSD/ports/distfiles/${DIST_SUBDIR}/
MASTER_SITE_OVERRIDE?= ${MASTER_SITE_BACKUP}
如果上边的不好使，就用163的。实际我用163也不错～
MASTER_SITE_BACKUP?=http://mirrors.163.com/FreeBSD/distfiles/${DIST_SUBDIR}/
MASTER_SITE_OVERRIDE?=${MASTER_SITE_BACKUP}

#### 6、安装软件
比如安装bash
cd /usr/ports/shells/bash
make install clean
chsh -s /usr/local/bin/bash
这样就可以了
如果不小心选错了安装选项，在install的时候报错，那么就删掉
/var/db/ports里边相应的文件

#### 7、top
大写P是现实多cpu
大写S是按照cpu使用率排序

#### 8、如何查找想要安装命令的软件包
cd /usr/ports
make search key=xxx

#### 9、修改alias
vi /root/profile
添加
alias cp='cp -i'
alias l.='ls -d .*'
alias ll='ls -l'
alias mv='mv -i'
alias rm='rm -i'

#### 10、安装vmtools
先要安装perl
cd /usr/ports/devel/p5-PPerl
make install clean
然后安装compat
cd /usr/ports/misc/compat6x
然后就跟linux安装vmtools没啥区别了

#### 11、安装fio
cd /usr/ports/sysutils/fio
make install clean
