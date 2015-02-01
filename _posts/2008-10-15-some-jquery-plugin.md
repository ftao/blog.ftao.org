---
title: 最近使用的几个jQuery 插件
author: Filia.Tao
layout: post
permalink: /2008/10/15/some-jquery-plugin/
categories:
  - 随便写写
tags:
  - DOMWindow
  - form
  - javascript
  - jquery
  - livequery
  - plugin
  - template
  - validate
---
最近使用了几个很好用的jQuery 插件, 在这里分享一下.

  * jquery.form.js <a href="http://www.malsup.com/jquery/form/" target="_blank">http://www.malsup.com/jquery/form/</a>  
    这个插件将表单ajax 化， 经常的做法是在beforeSubmit 函数里面做客户端数据验证， 在success 回调函数里面做提交成功的处理。
  * jquery.validate.js <a href="http://docs.jquery.com/Plugins/Validation" target="_blank">http://docs.jquery.com/Plugins/Validation<br /> </a>这个插件是用来做客户端数据验证, 很好很强大。 我用的时候在<a href="http://jquery.bassistance.de/validate/demo/milk/" target="_blank">这个页面</a>抄了很多， 是个好例子。
  * jquery.template.js <a href="http://plugins.jquery.com/project/jquerytemplate" target="_blank">http://plugins.jquery.com/project/jquerytemplate<br /> </a>这个是一个非常小巧的模板系统。 用来在客户端拼HTML 很好用， 至于说条件，循环等等不支持，我也不需要。  
    它默认的变量的格式是JSP 的格式，我自己写了一个smarty 格式的变量的正则</p> 
    <pre>smarty:     /\\{$([\w-]+)(?:\:([\w\.]*)(?:\((.*?)?\))?)?\}/g</pre>

  * jquery.livequery.js <a href="http://plugins.jquery.com/project/livequery" target="_blank">http://plugins.jquery.com/project/livequery</a>  
    这个我要严重推荐. 这个插件可以将事件绑定到某些选择器对应的元素上面。 即使页面元素已经发生变化， 绑定也会起作用。  
    至于说性能问题， 其实基本没有影响。 看一下作者的<a href="http://blog.brandonaaron.net/2007/08/19/new-plugin-live-query/" target="_blank">这篇Blog </a>就理解了。
  * jquery.DOMWindow.js  <a href="http://swip.codylindley.com/DOMWindowDemo.html" target="_blank"><img src="file:///C:/DOCUME~1/filia/LOCALS~1/Temp/moz-screenshot.jpg" alt="" /><img src="file:///C:/DOCUME~1/filia/LOCALS~1/Temp/moz-screenshot-1.jpg" alt="" />http://swip.codylindley.com/DOMWindowDemo.html</a>  
    用来做页面内的弹出窗口的， 类似thickbox ， 这个轻量级一点。 不过不得不说这个文档不好，试了好久才搞明白怎么用的。

就这么多了， jQuery 的插件还是挺多的挺好用的。
