---
layout: post
title: Nginx 通过FCGI支持Perl 脚本
created: 2010-08-07 17:23:35.000000000 +08:00
tags: web nginx
---

折腾了几天，总算让Nginx也支持Perl程序了。因为之前已经配置好PHP的环境了（请看<a href="http://nofool.info/content/gentoo%E4%B8%8B%E5%AE%89%E8%A3%85%E4%B8%8E%E9%85%8D%E7%BD%AEnginx">这里</a>），所以这一次只是想在原来的的基础上增加Perl的支持而已，所以并不打算需要太复杂的设置。在网上找了很久，都没有找到比较好的教程，很多都需要自己写一个接口程序和启动脚本，并且很多都是不太符合Gentoo启动脚本的格式。最后没办法，只有自己慢慢的摸索了，参照PHP的相关设置，绕了不少的弯路，才总算成功了。趁自己还没有忘记之前，把过程记录下来吧。

首先，需要的是一个Perl的接口程序，找了好久我才找到了<a href="http://nginx.localdomain.pl/wiki/FcgiWrap" target="_blank">fcgiwrap</a>这个程序，还好，Gentoo的Portage里也有，可以直接emerge它：

```bash
$ eix fcgiwrap
[*] www-misc/fcgiwrap
     Available versions:  (~)1.0.2-r1 (**)9999
     Installed versions:  1.0.2-r1(22时48分47秒 2010年08月05日)
     Homepage:            ht tp://nginx.localdomain.pl/wiki/FcgiWrap
     Description:         Simple FastCGI wrapper for CGI scripts (CGI support for nginx)
$ sudo emerge fcgiwrapp
```

创建一个spawn-fcgi启动脚本的链接

```bash
$ sudo ln -s /etc/init.d/spawn-fcgi  /etc/init.d/spawn-fcgi.pl
```

spawn-fcgi的配置文件

```bash
$ cat /etc/conf.d/spawn-fcgi.pl
# Copyright 1999-2009 Gentoo Foundation
# Distributed under the terms of the GNU General Public License v2
# $Header: /var/cvsroot/gentoo-x86/www-servers/spawn-fcgi/files/spawn-fcgi.confd,v 1.6 2009/09/28 08:38:02 bangert Exp $

# DO NOT MODIFY THIS FILE DIRECTLY! CREATE A COPY AND MODIFY THAT INSTEAD!

# The FCGI process can be made available through a filesystem socket or
# through a inet socket. One and only one of the two types must be choosen.
# Default is the inet socket.

# The filename specified by
# FCGI_SOCKET will be suffixed with a number for each child process, for
# example, fcgi.socket-1. 
# Leave empty to use an IP socket (default). See below. Enabling this, 
# disables the IP socket.
# 
FCGI_SOCKET=/var/run/fcgiwrap.sock

# When using FCGI_PORT, connections will only be accepted from the following
# address. The default is 127.0.0.1. Use 0.0.0.0 to bind to all addresses.
#
#FCGI_ADDRESS=127.0.0.1

# The port specified by FCGI_PORT is the port used
# by the first child process. If this is set to 1234 then subsequent child
# processes will use 1235, 1236, etc.
#
FCGI_PORT=

# The path to your FastCGI application. These sometimes carry the .fcgi
# extension but not always. For PHP, you should usually point this to
# /usr/bin/php-cgi.
#
FCGI_PROGRAM=/usr/sbin/fcgiwrap

# The number of child processes to spawn. The default is 1.
#
#FCGI_CHILDREN=1

# If you want to run your application inside a chroot then specify the
# directory here. Leave this blank otherwise.
#
#FCGI_CHROOT=

# If you want to run your application from a specific directiory specify
# it here. Leave this blank otherwise.
#
#FCGI_CHDIR=

# The user and group to run your application as. If you do not specify these,
# the application will be run as root:root.
#
FCGI_USER=nginx
FCGI_GROUP=nginx

# Additional options you might want to pass to spawn-fcgi
#
FCGI_EXTRA_OPTIONS="-M 0700"

# If your application requires additional environment variables, you may
# specify them here. See PHP example below.
#
ALLOWED_ENV="PATH"
```

Nginx配置文件

```bash
$ cat /etc/nginx/vhosts/example.com.conf
server {
    listen 127.0.0.1;
    server_name *.example.com example.com;
    access_log /var/log/nginx/localhost.access_log main;
    error_log /var/log/nginx/localhost.error_log info;
    root /var/www/example.com;

    location ~ .*.php$ {
	include /etc/nginx/fastcgi.conf;
	fastcgi_pass 127.0.0.1:1234;
	fastcgi_param  SCRIPT_FILENAME  /var/www/example.com/$fastcgi_script_name;
	fastcgi_index index.php;
    }
    
    location ~ .*.pl$ {
	include /etc/nginx/fastcgi_params;
	fastcgi_pass unix:/var/run/fcgiwrap.sock-1;
	fastcgi_param  SCRIPT_FILENAME  /var/www/example.com/$fastcgi_script_name;
	fastcgi_index index.pl;
    }
```

这里要注意的是，网上很多教程和fcgiwrap的手册（man page）都是把fastcgi_pass设置成 *fastcgi_pass unix:/var/run/fcgiwrap.sock* ，但是这样是不行的，查看Nginx的Log会发现有提示如下的错误：  

>connect() to unix:/var/run/fcgiwrap.sock failed (2: No such file or directory) while connecting to upstream, client: 127.0.0.1, server: *.example.com, request: "GET /test.pl HTTP/1.1", upstream: "fastcgi://unix:/var/run/fcgiwrap.sock:", host: "example.com"

原因嘛，/etc/conf.d/spawn-fcgi.pl 这个配置文件里关于FCGI_SOCKET的注释有写到，FCGI_SOCKET会为每个子进程添加一个数字后缀，所以需要把这个后缀也要添加上去，例如： *fastcgi_pass unix:/var/run/fcgiwrap.sock-1;* 否则，就会出现文件找不到的错误，我在这里花了不少的时间才明白这个事情。

最后，启动相关服务：

```bash
$ sudo /etc/init.d/spawn-fcgi.pl start
$ sudo /etc/init.d/nginx start
```

如果还是出现502错语并且Nginx Log 里有这样的出错提示：
>upstream closed prematurely FastCGI stdout while reading response header from upstream, client: 127.0.0.1, server: *.example.com, request: "GET /test.pl HTTP/1.1", upstream: "fastcgi://unix:/var/run/fcgiwrap.sock-1:", host: "example.com"  

那就要检查一下你的Perl文件是否有读取跟执行的权限了（755），在这里又花了我很长的时间才明白Perl脚本是还需要执行的权限的，这一点跟PHP不同。
