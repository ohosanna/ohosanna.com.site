---
layout: post
title: Rails3.1 invalid multibyte char错误处理
created: 2011-08-12 03:42:29.000000000 +08:00
tags: web rails
---

代码文件有中文字符，打开页面时报：invalid multibyte char (US-ASCII) 错误，造成这个错误是因为Ruby1.9默认用ASCII编码来读源码的，如果代码里有非ASCII字符，就会发生这个错误。解决的方法是在有非ASCII字符文件的**首行**指定encoding即可，如：

```ruby
# encoding: utf-8
class ApplicationController < ActionController::Base
  protect_from_forgery
  ...........
```

话说每一个文件都要加是不是太麻烦点？就不能弄一个全局设定来吗？
