---
layout: post
title: Gentoo下ROR rails/cli (LoadError)的解决
created: 2011-04-25 11:41:38.000000000 +08:00
tags: linux rails
---

想学习一下Ruby On Rail，结果安装完之后运行rails new demo新建新项目的时候出现以下错误：

```
/usr/lib64/ruby/site_ruby/1.8/rubygems/custom_require.rb:36:in `gem_original_require': no such file to load -- rails/cli (LoadError)
from /usr/lib64/ruby/site_ruby/1.8/rubygems/custom_require.rb:36:in `require'
from /usr/lib64/ruby/gems/1.8/gems/rails-3.0.3/bin/rails:8
from /usr/bin/rails:8:in `load'
from /usr/bin/rails:8
```

最后是通过

```bash
emerge -avD =dev-ruby/rack-mount-0.6.13 =dev-ruby/erubis-2.6.6 
```

把两个包降级后暂时解决这个问题，是新包有问题还是有其它的解决方法？
