---
layout: post
title: Gentoo Rails3.1 安装笔记
created: 2011-06-03 10:45:33.000000000 +08:00
tags: linux rails
---

Rails3.1的预览版已经出来了，带来了Sass和CoffeeScript这两个另人向往的新特性，因此打算在我的Gentoo上也安装来体验一下。

```bash
rvm use 1.9.2@railspre --create
gem update --system
gem update rake
gem install rails -v ">=3.1.0rc"
```

Rails3.1 需要Ruby >=1.9.2、Gem >=1.8.5、Rake >=0.9.1，所以在安装Rails3.1之前要先检查一下这几个条件是否已经达到。另外，现在网上看到的很多文章都说安装Rails3.1只需要：

```bash
gem install rails --pre
```

但这个方法从6月1日起就不行了，所以现在要改用：

```bash
gem install rails -v ">=3.1.0rc"
```

当使用rvm转到Ruby1.9.2的时候，会有一个问题：

```bash
gem -v
<internal:lib/rubygems/custom_require>:29:in `require': no such file to load -- auto_gem (LoadError)
	from <internal:lib/rubygems/custom_require>:29:in `require'
```

这是因为gem还在使用系统的库文件，而系统里的Ruby版本还是1.8.\*版的，所以就会有这个问题，解决的方法就是执行：
```bash
unset RUBYOPT
```

另外，使用Rails3.1的时候出现下面的错误：

```
ExecJS::RuntimeError
Could not find a JavaScript runtime</pre></p>
ExecJS::RuntimeError
```

这是因为系统里没有JS的运行环境，只要手动安装一个就可以了：

```bash
emerge nodejs
```
