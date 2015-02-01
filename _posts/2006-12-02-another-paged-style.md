---
title: 另一种分页显示的方法
author: Filia.Tao
layout: post
permalink: /2006/12/02/another-paged-style/
categories:
  - python
---
> 最近用[Karrigell ][1]写一个小东西.就像在PHP中一样遇到了分页显示的问题.

> 这次我跟平时不同,不采用根据现在的页码生成前一页 后一页的页码.而是采用类似 show?next ,show?pre ,show?first 形式的链接来翻页.

> 
<pre>sess = Session();

try:
argument = THIS.path.split('?',1)[1]
if argument == "pre":
sess.page = sess.page - 1
elif argument == "next":
sess.page = sess.page + 1
elif argument == "first"        :
sess.page = 1
else:
sess.page   #no change

except (NameError,AttributeError,IndexError):
sess.page = 1</pre>

链接HTML代码

<pre>[<a href="http://www.iblog.com/status?first">First Page</a>]  [<a href="http://www.iblog.com/status?pre">Previous Page</a>]  [<a href="http://www.iblog.com/status?next">Next Page</a>]</pre>

关键在于不在链接中使用绝对的页码,而用Session来保存当前页码.

当然这会在导致无法用链接的形式来引用某一页.

这个在很多情况不是问题,因为这个链接本没有意义,第n页的内容可能随时在变.

 [1]: http://karrigell.sourceforge.net/ "A flexible Python web framework"