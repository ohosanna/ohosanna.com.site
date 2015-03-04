---
layout: post
title: 修复因ImageMagick版本升级后导致的RMagick错误
created: 2012-08-25 09:33:14.000000000 +08:00
excerpt_separator: <!--more-->
tags: ruby
---
前段时间升级了系统的各个软件包，今天发现运行一个 Rails 项目的时候报如下错误:  

```bash
This installation of RMagick was configured with ImageMagick 6.7.5 but ImageMagick 6.7.6-4 is in use
```

<!--more-->

造成这个问题是因为 ImageMagick 是通过系统本身的包管理器来安装的（如 Gentoo 的 Portage ），而 RMagick 则是通过 Gem 来安装。当我通过系统的包管理器升级了 ImageMagick，Gem管理的RMagick却没有自动更新，于是导致了两者相依赖的库的版本不一致就产生了以上的错误。

有两个办法可以解决这个问题：

1. 将 ImageMagick 降级到与 RMagick对应的版本。
2. 重新安装/升级 RMagick。

由于 ImageMagick 的块头不小，编译起来比较花时间（当然如果用的是二进制包的发行版，就可以不用考虑这个问题，对Gentoo党的我来说，这是个问题。），所以重新安装/升级体积更小的 RMagick 当然是首选啦。简单的执行以下命令即可：

```bash
gem install rmagick -v 2.13.1
```
