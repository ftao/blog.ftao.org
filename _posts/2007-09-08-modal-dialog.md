---
title: Modal Dialog
author: Filia.Tao
layout: post
permalink: /2007/09/08/modal-dialog/
categories:
  - javascript
tags:
  - blockUI
  - dialog
  - javascript
  - jquery
  - modal
---
我也写了个基于jQuery 和 blockUI 的Modal Dialog 组件.  
其实已经有一个 jqModal 了 , 但是它的使用风格我不喜欢,而且比较难看.  
昨天自己写了一个, 编程风格有点象XUL的dialog.

  * 可以指定显示的按钮,按钮标签,回调函数.
  * 可以指定标题
  * 实现了Dialog.alert , Dialog.confirm , Dialog.prompt 这三个常用功能.

总的来说, blockUI 比较灵活, 但是显示/隐藏的代码分散在不同的地方, 有点混乱.  
我的Dialog 基于回调函数, 同时显示/隐藏自动完成.  
可以通过在回调函数中返回false,来阻止默认行为隐藏对话框.  
下面是代码, 这里我使用More 标签. (由于使用js作语法高亮,只有在网页上才能正常浏览)  
* (如果你想查看这个东东的最新版本, 请查看<a href="http://s5-creator.googlecode.com/svn/trunk/HTML/js/jquery/dialog.js" target="_blank">这里</a>)* <!--more-->

[CODE::javascript/dialog.js::JScript/]
