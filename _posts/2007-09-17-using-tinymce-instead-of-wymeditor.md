---
title: 放弃WYMEditior,改用TinyMCE
author: Filia.Tao
layout: post
permalink: /2007/09/17/using-tinymce-instead-of-wymeditor/
categories:
  - Web开发
tags:
  - web-tinymce-wymedtior-S5-Creator
  - Web开发
---
最后还是决定放弃WYMEditor , 使用TinyMCE. 可惜了很多花在放在改造WYMEditor上的时间.  
放弃的原因,简单来说它还不够成熟, 一些特性不是我需要的. 具体来说:

  * 浏览器支持问题. jquery 支持opera ,但是它不完全支持.
  * 发现我要的还是WYSIWYG , 不是 WYMIWYG. 它的一些特性用不到.
  * TinyMCE 更成熟, 文档详细. 而且也能产生高质量的代码. 除了大了一点, 不是用jquery 写的 (应该也不会有冲突)外, 完全符合我的需要.

开始转换成TinyMCE.