---
layout: post
title: iframe 自适应高度
created: 2010-07-15 17:01:35.000000000 +08:00
tags: web
---

今天碰到一个客户要求在他网站上添加背景音乐，还是要求切换栏目的时候不间断那种。囧……我最讨厌就是浏览有背景音乐的网站了，但好像国内的小企业客户就喜欢这样来。没办法，要不间断播放我只能想到用框架大法了。

但是，用iframe的时候却碰到了个问题：

```html
<iframe src="source.html" width="100%" scrolling="auto" frameborder="0" height="100%">
</iframe>
```

这样设置的时候，在FF下是OK的，但是在IE下，却是有两个滚动条，这并不是我想要的效果。

最后，在网上找了一段JS代码，将iframe的高度自动设置，问题得到解决。

```javascript
<script type="text/javascript">
 function SetCwinHeight(){
  var iframeid=document.getElementById("web"); //iframe id
  if (document.getElementById){
   if (iframeid && !window.opera){
    if (iframeid.contentDocument && iframeid.contentDocument.body.offsetHeight){
     iframeid.height = iframeid.contentDocument.body.offsetHeight;
    }else if(iframeid.Document && iframeid.Document.body.scrollHeight){
     iframeid.height = iframeid.Document.body.scrollHeight;
    }
   }
  }
 }
</script>
<iframe id="web" onload="Javascript:SetCwinHeight()" src="source.html" width="100%" scrolling="no" frameborder="0" height="87">
</iframe>
```

