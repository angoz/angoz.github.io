---
layout: post
title: Install Yaf extension for XAMPP-LINUX 
date: 2012-08-02 17:41:05
categories:
 - developer
tags:
 - server
description: Install Yaf for XAMPP-LINUX
---

### 0. Env
CentOS Linux release 6.0 (Final)

### 1.Rready
\# cd ~
\# curl -O http://tenet.dl.sourceforge.net/project/xampp/BETAS/xampp-linux-1.8.0.tar.gz
\# curl -O http://tenet.dl.sourceforge.net/project/xampp/BETAS/xampp-linux-devel-1.8.0.tar.gz
\# curl -O http://pecl.php.net/get/yaf-2.1.18.tgz

### 2.Install

##### 2.1 install XAMPP & XAMPP DEVELOP
\# mkdir /opt
\# tar -zxvf xampp-linux-1.8.0.tar.gz -C /opt
\# tar -zxvf xampp-linux-devel-1.8.0.tar.gz -C /opt

##### 2.2 install Yaf
\# cd ~
\# tar -zxvf yaf-2.1.18.tgz
\# cd yaf-yaf-2.1.18
\# /opt/lampp/bin/phpize
\# ./configure --with-php-config=/opt/lampp/bin/php-config
\# make && make install

and we will get the extension path, such as:
/opt/lampp/lib/php/extensions/no-debug-non-zts-20100525/

\# vim /opt/lampp/etc/php.ini
\# /\extension_dir

set the extension path:
extension_dir = "/opt/lampp/lib/php/extensions/no-debug-non-zts-20100525/"
extension="yaf.so"

save and quit php.ini:
\# :x

#### 3.Start
\# /opt/lamp/lamp start

#### 4.Check
\# cd /opt/lampp/htdocs
\# vim phpinfo.php

<?php
phpinfo();
?>

open your browser, and visit the site phpinfo.php, and check yaf is success for installing.
