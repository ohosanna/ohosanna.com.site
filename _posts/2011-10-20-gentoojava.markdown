---
layout: post
title: Gentoo下Java程序中文显示为方框的处理
created: 2011-10-20 17:48:46.000000000 +08:00
tags: linux
---

最近在捣鼓Pre的时候碰到了一个问题：在我的Gentoo下，java程序的中文都显示为方框，如下图：  
![java程序中文显示错误图片"](http://pic.yupoo.com/ohosanna_v/Btr17pkv/grear.png)

最后，在网上找到了解决方法：在/etc/java-config-2/current-system-vm/jre/lib下面建立目录fonts/fallback

```bash
mkdir -p  /etc/java-config-2/current-system-vm/jre/lib/fonts/fallback
```

然后在这个文件夹里创建一个中文字体的链接：

```bash
ln -s /usr/share/fonts/wqy-zenhei/wqy-zenhei.ttc  /etc/java-config-2/current-system-vm/jre/lib/fonts/fallback/
```

这样java程序就可以正常的显示中文了。  
![java程序中文显示正常图片](http://pic.yupoo.com/ohosanna_v/Btr186s5/l6UWk.png)
