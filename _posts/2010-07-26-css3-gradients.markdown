---
layout: post
title: 理解CSS3渐变背景
created: 2010-07-26 00:50:06.000000000 +08:00
tags: web
---

CSS3 支持定义渐变的背景，这个功能很实用，今天试用了一下，大概了解了基本的设置方式。

####Webkit 类浏览器设置

```css
/*例子*/
background: -webkit-gradient(linear, 0 0, 0 100%, from(red), to(blue));
```

* 第一个参数指定渐变的类型，linear（线性）或 radial（径向）。
* 第二个参数指定渐变开始的坐标，可以是数字或百分比，也可以是关键字（如 left-top ）。
* 第三个参数指定渐变结束的坐标。
* 第四个参数指定渐变开始的颜色。
* 第五个参数指定渐变结束的颜色。

####Firefox 浏览器设

```css
/*例子*/
background: -moz-linear-gradient(top, red, blue);
```

* 第一个参数指定从哪里开始渐变（ 例子里用的是 top ，还可以是指定角度如-45deg ）。
* 第二个参数指定渐变开始的颜色（red）。
* 第三个参数指定渐变结束的颜色（blue）。

####效果

<div style="width: 300px; height: 150px; background: -moz-linear-gradient(center top , red, blue) repeat scroll 0% 0% transparent;">
</div>

参考链接：  
<a href="http://webkit.org/blog/175/introducing-css-gradients/">http://webkit.org/blog/175/introducing-css-gradients/</a>  
<a href="http://hacks.mozilla.org/2009/11/css-gradients-firefox-36/">http://hacks.mozilla.org/2009/11/css-gradients-firefox-36/</a>
