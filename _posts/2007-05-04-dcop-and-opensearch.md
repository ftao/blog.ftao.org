---
title: DCOP and OpenSearch
author: Filia.Tao
layout: post
permalink: /2007/05/04/dcop-and-opensearch/
categories:
  - Linux
  - Web开发
tags:
  - Linux
  - web
---
<a href="http://developer.kde.org/documentation/other/dcop.html" title="DCOP document" target="_blank">DCOP</a>  (Desktop COmmunications Protocol)是KDE下面应用程序相互通讯的协议。  
我是这几天写一个amarok的脚本的时候发现这个东西。  
对我来说这个协议本身没有什么奇妙的地方，关键是我们可以通过直接在终端上敲命令来控制应用程序。  
比如

> dcop 列出所有可用的应用程序  
> dcop amarok 列出可用的子模块 (比如collection,playlist)  
> dcop amarok playlist 列出针对amarok的播放列表可用的函数  
> dcop amarok player next 播放下一首。

很好玩，这种机制就可以很容易的脚本语言来实现一些有趣的功能。

比如，我正在写从网络上抓专辑封面图片的脚本。  
我现在使用douban 的数据。why?  
你可能没有注意到在douban的搜索页面的右下角有一个淡淡的OpenSearch RSS 2.0.  
实际上你在浏览器的地址栏输入

> http://www.douban.com/opensearch?q=google

你就可以得到跟google有关的douban上的item.关键是它的输出是一个规范的RSS 2.0 文件。  
这样我们就可以可靠的分析这个XML文件来抓取相关信息。（而不是从一个复杂的HTML页中有正则表达式去抓信息）  
标题中两个东西，看似没什么关系。但是他们同样提供了一种方便，可靠的接口来访问一个复杂的系统。