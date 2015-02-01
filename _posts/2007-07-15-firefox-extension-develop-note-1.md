---
title: Firefox扩展开发笔记(1)
author: Filia.Tao
layout: post
permalink: /2007/07/15/firefox-extension-develop-note-1/
categories:
  - Firefox
tags:
  - Firefox
---
下面纯粹是一点经验, 有些可能并不严密.

  1. overlay 中 command 一定要放在<commandset id=”mainCommandSet”>中 , 否则很可能用不起来.
  2. 几个全局的变量 gContextMenu, gBrowser 等等 (chrome://chrome://browser/content/browser.js)
  3. 动态菜单 (使用 gContentMenu.showItem显示,隐藏菜单项)
  4. 使用browser element 来在后台载入文档.  
    (http://filiasoc.blogspot.com/2007/07/implemnet-of-index-this-link.html)
  5. 无数扩展的源代码就是最好的参考资料.

<pre></pre>
