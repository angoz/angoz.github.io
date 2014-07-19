---
layout: post
title: Linux Memcache 启动脚本
date: 2012-05-15 12:58:33
categories:
 - developer
tags:
 - server
 - linux
 - shell
description: 简单易用的服务端 Memcache 启动脚本
---

相关的 shell 代码如下，其中相应的 memcache 路径请自行替换。

{% highlight shell linenos %}
#!/bin/sh   
#   
# memcached:    MemCached Daemon   
#   
# chkconfig:    - 90 25    
# description:  MemCached Daemon   
#   
# Source function library.   
. /etc/rc.d/init.d/functions
. /etc/sysconfig/network
    
start()
{
        echo -n $"Starting memcached: "
        daemon /usr/local/bin/memcached -u daemon -d -m 4096 -l 10.10.10.220 -p 58728
        echo
}   

stop()
{
        echo -n $"Shutting down memcached: "
        killproc memcached
        echo
}   
    
[ -f /usr/local/bin/memcached ] || exit 0

# See how we were called.
case "$1" in
  start)
        start
        ;;
  stop)
        stop
        ;;
  restart|reload)
        stop
        start
        ;;
  condrestart)
        stop
        start
        ;;
  *)
        echo $"Usage: $0 {start|stop|restart|reload|condrestart}"
        exit 1
esac
exit 0
{% endhighlight %}

P.S.
chmod +x /etc/init.d/memcached
chkconfig --level 2345 memcached on
service memcached start

ubuntu、debain:
apt-get -y install chkconfig
