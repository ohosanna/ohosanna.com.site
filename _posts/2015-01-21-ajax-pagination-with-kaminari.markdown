---
layout: post
title: "Kaminari 的 AJAX 翻页实现"
date: "2015-01-21 16:15:46 +0800"
excerpt_separator: "<!--more-->"
tags: rails web
---

[Kaminari](https://github.com/amatsuda/kaminari) 是 rails 上一个很有名的分页管理扩展，使用极其简单。最近刚在项目中使用了它的 AJAX 分页的功能，记录一下实现的过程。

基本的安装和配置就不啰嗦了，按照官方的教程来进行就是啦，具体记录一下 AJAX 翻页实现的部分，以一个订单列表的页面作例子。
<!--more-->

####View
>app/views/orders/index.html.haml

```haml
.table-resonsive
  - if @orders.nil?
    暂时还没有订单记录
  - else
    %table#list-orders.table.table-vcenter
      %thead
        %tr
          %th.order-id 订单号
          %th.order-track 订单状态
          %th.order-actions 操作
      %tbody
        = render partial: 'list_order', collection: @orders, as: :order
#paginator
  = paginate @orders, remote: true
```
>app/views/orders/\_list\_order.html.haml

```haml
%tr{ :id => "order-#{order.id}" }
  %td= order.id
  %td
    .status
  %td
    =link_to "更新状态", edit_cpanel_order_path(order), :remote => true
```

>app/views/orders/index.js.erb

```javascript
$("#list-orders tbody").html("<%= j( render partial: 'list_order', collection: @orders, as: :order ) %>")
$("#paginator").html("<%= j( paginate(@orders, :remote => true).to_s ) %>")
```

####Controller
>app/controller/orders_controller.rb

```ruby
def index
  @orders = Order.all.order_by(:_id => :desc ).page params[:page]
  @status = OrderStatus.enabled
end
````

这样很简单就可以实现了AJAX的翻页效果
