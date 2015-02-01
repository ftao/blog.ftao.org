---
title: 再谈在线幻灯片服务-编辑器
author: Filia.Tao
layout: post
permalink: /2007/10/02/online-presentation-editor/
categories:
  - Web开发
tags:
  - editor
  - slide
---
主要是谈谈编辑器的问题. 主要有两类, 基于Flash / 基于HTML/AJAX 的, 基于Flash 的我不懂, 就不说了.  
我尝试了很多现有的在线幻灯片服务, 比如Zoho , Google Presentation 等, 发现

  * 编辑器都没有使用document 的designMode 来实现.
  * 都没有查看/编辑源码功能.
  * 都提供可改变大小的文本框来输入文字
  * 都提供类似层的概念, 内容可以层叠.

这样做的原因我想大概是符合传统的桌面软件的使用风格. 但是这样的情况下, 不能使用很多现成的WYSIWYG 编辑器(都是基于designMode 的), 自己设计这样的一个编辑器会比较复杂.

现在比较迷茫, 到底是使用那种方式呢?