---
title: Firefox的界面
author: Filia.Tao
layout: post
permalink: /2007/07/06/firefox-ui-xul/
categories:
  - Firefox
tags:
  - extension
  - Firefox
  - xul
---
[声明] 本文是关于Firefox 扩展开发的.  
Firefox 的界面使用XUL来表示.  
在地址栏中输入 chrome://browser/content/browser.xul, 你可以看见又一个firefox.  
我们来看一下firefox 的主窗口有那些部分组成.  
打开DOM查看器,我们可以看见browser.xul的DOM树,展开window节点,我们可以看见.

  * 若干个script节点 这些都是firefox使用的脚本.
  * 若干个stringbundleset , 这些文件用于国际化,里面包含javascript 使用的字符串
  * 若干个commandset
  * 若干个popupset , 弹出菜单
  * toolbox, toolbar 工具栏. 具体作用看它们的ID就可以猜出来了.
  * statusbar 状态栏
  * stack(id=browser-stack) 这个很重要, 就是浏览器的主窗口.可以看见里面包括侧边栏(sidebar-box),和主窗口(appcontent).  tabbrowser (id=content)我们在扩展一般就通过这个访问当前的打开标签页组.
  * 还有其他一下元素的功能我还没有彻底搞明白.

我们在扩展开发中使用overlay 的方法,来在firefox界面上加上我们自己的元素. 使用javascript来操纵界面. (使用DOM方法就可以了,就像操纵普通的HTML页一样).