---
layout: post
title: margin-top 在Firefox和IE8下的问题
created: 2010-06-17 00:48:16.000000000 +08:00
tags: web
---

最近，在写CSS样式的时候总碰到一个问题（其实也不是最近才碰到，只不过是最近才比较在意而已），那就是当我对一个层用了margin-top这个属性的时候，这个层本身并没有如预期一样起作用，反而是它的父级应用了这个属性！如下图：

<a href="/sites/default/files/u1/firefox-margin-top-bug.jpg">
<img alt="firefox-margin-top-bug" src="/sites/default/files/u1/firefox-margin-top-bug.jpg" style="border: 0px solid; margin: 0px; width: 600px;" /></a>

<!-- more -->

HTML代码：

```html
<div id="header">
  <div id="header-inner" class="clear-block"> 
    <div id="site-title">
      <div id="logo-title">
		    <h1 id="site-name"><a href="/" title="首页" rel="home">老农家的一亩三分地</a>
       </h1>
	    </div> <!-- /#logo-title -->
	 </div>
</div>
</div>
```

CSS代码：

```css
#header-inner {
    width: 970px;
    height: 270px;
    clear: both;
    margin: 0 auto;
    background: url(images/head.jpg) no-repeat center top;
}

#site-title {
    width: 870px;
    height: 30px;
    margin: 0 auto;
    margin-top: 160px;
}
```

其实，我的本意是让网站的标题跟顶部有一定的距离，好让标题(#site-title)可以显示在正确的位置，但现在的结果是标题的margin-top不起作用，反而是他的父级(#header)应用了这个属性。这个问题在IE6下不会出现，但在IE8和Firefox等一些“现代”的浏览器下会出现，这就奇怪了，一般来说平时碰到的问题都是因为IE6的问题，现在反而倒回来了，难道是CSS的一个Bug?

最后，还是在网上找到了一个临时的解决方案，那就是在这个层（#site-title）的父级（#header-inner）添加一个padding-top的值就可以了，哪怕是1个像素！到底是不是CSS的一个Bug就不清楚了，不过这确实解决了我的问题。

CSS代码

```css
#header-inner {
    width: 970px;
    height: 269px;
    clear: both;
    margin: 0 auto;
   padding-top: 1px;
    background: url(images/head.jpg) no-repeat center top;
}

#site-title {
    width: 870px;
    height: 30px;
    margin: 0 auto;
    margin-top: 160px;
}
```

最终的结果如下：  
<a href="/sites/default/files/u1/firefox-margin-top-fix.jpg">
<img alt="firefox-margin-top-fix" src="/sites/default/files/u1/firefox-margin-top-fix.jpg" style="border: 0px solid; margin: 0px; width: 600px;" /></a>
