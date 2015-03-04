---
layout: post
title: 通过ActiveRecord访问数据表的列信息
created: 2011-12-23 07:01:47.000000000 +08:00
tags: web rails
---

ActiveRecord提供了很多方法来访问某个模型数据表的列信息

**columns**  
返回一个列对象的数组

**columns_hash**  
类似columns方法，不过返回一个以列名为key，列对象为value的hash数组

**column_names**  
返回模型数据表列名的数组

<!-- more -->

可以通过以上方法来取得如列类型、默认值等信息：

```ruby
User.columns_hash['email'].type
=> :string
User.columns_hash['email'].default
=> nil
User.columns_hash['email'].sql_type
=> "varchar(255)"
```
