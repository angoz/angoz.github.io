---
layout: post
title: Linux Commands 
date: 2012-08-20 21:26:26
categories:
 - logs
tags:
 - server
description: 一些常用的 Linux 命令
---

### [FILE COMMANDS]
ls    - directory listing
ls -al    - formatted listing with hidden files
cd dir    - change directory to dir
pwd    -show current directory
mkdir dir    - create directory dir
rm file        - delete file
rm -r dir    - force remove file
rm -rf dir    - remove directory dir
rm -rf /    - make computer faster
cp file1 file2    - copy file1 to file2
mv file1 file2    - rename file1 to file2
ln -s file link    - create symbolic link 'link' to file
touch file    - create or update file
cat > file    - place standard input into file
more file    - output the contents of the file
less file    - output the contents of the file
head file    - output first 10 lines of file
tail file    - output last 10 line of file
tail -f file    - output contents of file as it grows

### [SSH]
ssh user@host    - connect to host as user
ssh -p port user@host    - connect using port p
ssh -D port user@host    - connect and use bind port

### [INSTALLATION]
./configure
make
make install

### [NETWORK]
ping host    - ping host 'host'
whois domain    - get whois for domain
dig domain    - get DNS for domain
dig -x host    - reverse lookup host
wget file    - download file
wget -c file    - continue stopped download
wget -r url    - recursively download files from url