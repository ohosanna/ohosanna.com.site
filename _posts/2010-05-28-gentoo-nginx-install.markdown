---
layout: post
title: Gentoo下安装与配置Nginx
created: 2010-05-28 00:46:15.000000000 +08:00
tags: linux nginx
---

####1、安装Nginx

为Nginx加入fastcgi(用来支持PHP)和ssl(支持https加密链接)USE标记：

```bash
echo "www-servers/nginx fastcgi ssl" >> /etc/portage/package.use
```

然后emerge nginx:

```bash
emerge -av nginx
```
####2、配置Nginx

Nginx的配置文件位于 /etc/nginx/下。(/etc/nginx/nginx.conf是主要的配置文件)

通常需要修改的是服务器配置部分：
> /etc/nginx/nginx.conf

```
server {
listen          80;
server_name     www.example.com;
access_log      /var/log/nginx/www.example.com.access_log main;
error_log       /var/log/nginx/www.example.com.error_log info;
root /var/www/www.example.com/htdocs;
}
```

####配置 Fast CGI

如果站点是只有html的静态文件，可以不用CGI支持，如果你想使用动态脚本（如PHP），则要通过CGI来实现。Nginx并不直接支持CGI，因此需要安装一个辅助程序将CGI的输出结果返回给Nginx，这里使用spawn-fcgi。

spawn-fcgi还未进入稳定分支，因此要先加入keywords

```bash
echo www-servers/spawn-fcgi ~amd64 >> /etc/portage/package.keywords
```

然后就可以emerge它了：

```bash
emerge spawn-fcgi
```

安装PHP并加入cgi和force-cgi-redirect支持。

```bash
echo dev-lang/php cgi force-cgi-redirect >> /etc/portage/package.use
emerge php
```

为Spawn-fcgi创建一个启动脚本链接

```bash
ln -sf /etc/init.d/spawn-fcgi /etc/init.d/spawn-fcgi.php
```

创建spawn-fcgi.php配置文件：

```bash
cp /etc/conf.d/spawn-fcgi /etc/conf.d/spawn-fcgi.php
```

配置文件内容：
> /etc/conf.d/spawn-fcgi.php

```
# Copyright 1999-2004 Gentoo Foundation

# Distributed under the terms of the GNU General Public License v2

# $Header: /var/cvsroot/gentoo-x86/www-servers/lighttpd/files/spawn-fcgi.confd,v 1.1 2005/02/14 11:39:01 ka0ttic Exp
# $Configuration file for the FCGI-Part of /etc/init.d/lighttpd

## Set this to "yes" to enable SPAWNFCGI
ENABLE_SPAWNFCGI="yes"

## ABSOLUTE path to the spawn-fcgi binary
SPAWNFCGI="/usr/bin/spawn-fcgi"

## ABSOLUTE path to the PHP binary
FCGI_PROGRAM="/usr/bin/php-cgi"

## bind to tcp-port on localhost
FCGI_PORT="65532"

## number of PHP childs to spawn
PHP_FCGI_CHILDREN=5

## number of request server by a single php-process until is will be restarted
PHP_FCGI_MAX_REQUESTS=1000

## IP adresses where PHP should access server connections from
FCGI_WEB_SERVER_ADDRS="127.0.0.1"

# allowed environment variables sperated by spaces
ALLOWED_ENV="PATH USER"

# do NOT change line below
ALLOWED_ENV="$ALLOWED_ENV PHP_FCGI_MAX_REQUESTS FCGI_WEB_SERVER_ADDRS"

## if this script is run as root switch to the following user
USERID=nginx
GROUPID=nginx
```

通常要配置的选项是 FCGI_PROGRAM, FCGI_PORT, USERID and GROUPID。

FCGI_PROGRAM : CGI程序的绝对路径。

FCGI_PORT : Nginx监听的端口，可以随便定义，注意不要和其它服务有冲突。

USERID 和 GROUPID ：以什么用户和用户组来运行。

配置好后就可以启动这个服务和把它加入到开机自动运行。

```bash
/etc/init.d/spawn-fcgi.php start
rc-update add spawn-fcgi default
```

在Nginx的配置文件加入fastcgi支持，加入以下内容：
> /etc/nginx/nginx.conf 

```
index index.php index.html index.htm default.html default.htm
location ~ .*.php$ {
    include /etc/nginx/fastcgi.conf;
    fastcgi_pass  127.0.0.1:65532;
    fastcgi_index index.php;
}
```
fastcgi_pass 是设置监听 spawn-fcgi 的地址和端口，注意这里的端口应该跟刚才在/etc/conf.d/spawn-fcgi.php里设置的一致。

启动Nginx服务:

```bash
/etc/init.d/nginx start
```

或者把Nginx加入开机启动：

```bash
rc-update add nginx default
```

至此，Nginx的安装完成。

参考：
[Gentoo wiki](http://en.gentoo-wiki.com/wiki/Nginx)
