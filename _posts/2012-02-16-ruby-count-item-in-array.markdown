---
layout: post
title: 计算一个数组里各元素出现的次数（ruby）
created: 2012-02-16 06:44:33.000000000 +08:00
tags: ruby
---

一个包含了多个元素的数组，如何计算出这个数组里各个元素出现的次数呢？比如说：  
给出： `a = ['cat','dog','fish','fish']`  
得到的最终结果： `a2 = {'cat' => 1, 'dog' => 1, 'fish' => 2}`

<!-- more -->

解决这个问题的方法有很多种，以下是用Ruby语言来实现的几个方法：

方法一：  
```ruby
a = ['cat','dog','fish','fish']
a2=Hash[a.group_by {|x| x}.map {|k,v| [k,v.count]}]
```

方法二：  
```ruby
a = ['cat','dog','fish','fish']
a2 = a.reduce(Hash.new(0)) { |a, b| a[b] += 1; a }
```

方法三：  
```ruby
#适合Ruby 1.9.2以上版本
a = ['cat','dog','fish','fish']
a2 = a.each_with_object(Hash.new(0)) { |animal, hash| hash[animal] += 1 } 
```

方法四：  
```ruby
a = ['cat','dog','fish','fish']
a2 = a.uniq.inject({}){|h, e| h[e] = a.count(e); h }
```

方法五：  
```ruby
a = ['cat','dog','fish','fish']
a2 = Hash[a.uniq.map {|i| [i, a.count(i)]}]
```

方法六：  
```ruby
a = ['cat','dog','fish','fish']
a2 = {}
a.uniq.each{|e| a2[e]= a.count(e)}
```

就我个人而言，第五种方法是我能想到的也是比较可以理解的，其它的方法有些还是未能完全理解，果然还是很多东西都不懂啊！
