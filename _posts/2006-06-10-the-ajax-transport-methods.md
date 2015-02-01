---
title: The Ajax Transport Method(s)
author: Filia.Tao
layout: post
permalink: /2006/06/10/the-ajax-transport-methods/
categories:
  - javascript
  - Web开发
---
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 今天在IBM DevelopNetwork 上看到这篇文章 [The Ajax Transport Method(s)][1]

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 文中谈论三中在AJAX应用中使用的数据传输手段，通过XMLHttpRequest,通过Frame或IFrame,通过<script>标记来加载一个js文件。  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 对于第三种，我发现在小百合([bbs.nju.edu.cn][2])的首页运用了很多。它的首页导读的很多数据都是通过js文件来获得的。第三种方式的优点是可以跨域读取。  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 其他两种方式比较常见。  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Frame或IFrame 是DHMTL时代的常见手段。比如说基于smth bbs 代码的bbs首页的左边 的树状讨论区列表，打开下级目录时会有一个"载入中",就是通过IFrame从服务器读取数据。  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; XMLHttpRequest 就不用说了，AJAX 中的同服务器交互的标准方法。  
&nbsp;

 [1]: http://ajaxian.com/archives/the-ajax-transport-methods
 [2]: http://bbs.nju.edu.cn
