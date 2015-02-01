---
title: WYSIWYG 编辑器的原理
author: Filia.Tao
layout: post
permalink: /2007/08/25/wysiwyg-editor-principle/
categories:
  - javascript
  - Web开发
  - 编程开发
tags:
  - design-mode
  - IE
  - javascript
  - Web开发
  - WYSIWYG
  - WYSIWYM
  - 所见即所得编辑器
  - 编程开发
---
以前写过这篇文章, <a href="http://blog.ftao.org/2006/10/31/wysiwyg-editors/" rel="bookmark" title="到《几个WYSIWYG编辑器》的永久链接">几个WYSIWYG编辑</a>. 一致很好奇, 这样的可视化编辑器是如何实现? 功能那么多. 做了一点研究,发现事情并非如有原来所想. WYSIWYG编辑功能其实不是由这些编辑器来实现的,而是由浏览器自己实现, 这些编辑器只是进行了一些封装. (屏蔽不同浏览器的不同实现, 提供工具栏等等.)

  * 打开浏览器的编辑模式, 只要将要编辑的文档设置为 document.designMode = “On” .  
    访问<a href="https://www.squarefree.com/bookmarklets/misc.html#edit_page" target="_blank">这里</a> ,添加启用designMode的书签, 访问页面是点击书签就可以编辑页面了.
  * 大部分WYSIWYG 编辑器的功能, 比如自动加入回车自动加入<p>.列表下回车, 新增项目等都是浏览器实现的. 这就解释了为什么几乎所有的编辑器都是同样的行为.
  * IE可以实现仅对某一个元素进行编辑.
  * IE在designMode 下所有元素都可见 , 也就是 display:none不会有效果. (这个昨天折腾了我一个晚上, <a href="http://dev.filia.cn/s5-creator/test/ie_design_mode_test.htm" title="测试IE的DesignMode" target="_blank">测试页面</a>(当然请用IE打开测试))
  * IE 中指定了高度(宽度?)的元素会出现边框, 可以调整大小.

我现在在研究<a href="http://www.wymeditor.org/en/" target="_blank">WYMeditor </a>(What You See is What You Mean ), 这个编辑器可以产生质量比较好的代码, 不会插入font 那种垃圾代码, 通过添加className 的方式格式化文本.
