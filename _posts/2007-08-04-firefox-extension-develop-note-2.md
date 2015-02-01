---
title: Firefox扩展开发笔记(2)
author: Filia.Tao
layout: post
permalink: /2007/08/04/firefox-extension-develop-note-2/
categories:
  - Firefox
  - javascript
tags:
  - Firefox
  - javascript
---
  1. 处理书签.  
    我要做的工作是索引书签. (<a href="http://browser-extension-for-beagle.googlecode.com/svn/trunk/firefox-extension@beagle.org/chrome/content/indexBookmark.js" target="_blank">indexBookmark.js</a>)  
    我的主要的学习资料是几个已经存在的扩展。</p> 
      * http://memotoofirefox.mozdev.org/　最推荐。代码写的很有参考价值。
      * http://bookmarkdd.mozdev.org/
      * 还有其他很多书签扩展，都是可以看一下的。
    
    还有其他链接有
    
      * <http://www.xulplanet.com/references/elemref/ref_rdfbookmarks.html>
      * chrome://browser/content/bookmarks/bookmarks.js
      * chrome://browser/content/bookmarks/bookmarksMenu.js
    
    总的来说Firefox的书签是RDF 数据源,要学一点RDF的知识. 上面两个chrome 文件提供了一些方法来操作书签,也提供了一些全局变量和常量. (这两个文件firefox 默认好像是不载入的,手工载入)</li> 
    
      * get 语法 .一直想写, 但又没找到权威的资料(其实是没找). 不过用的很多.  
        var o = {  
        get att() {return “test get “;},  
        }  
        alert(o.att  == “test get”)</ol>