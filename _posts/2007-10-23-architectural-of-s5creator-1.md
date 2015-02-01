---
title: 'S5Creator &#8211; 架构与设计模式(1)'
author: Filia.Tao
layout: post
permalink: /2007/10/23/architectural-of-s5creator-1/
categories:
  - javascript
  - Web开发
  - 编程开发
tags:
  - design-pattern
  - javascript
  - S5Creator
---
S5Creator 是我最近在做的一个项目.

基于Web的S5 幻灯片创作工具  
主页: <a href="http://code.google.com/p/s5-creator/" target="_blank" title="外部链接至 http://code.google.com/p/s5-creator/" class="externalLink">http://code.google.com/p/s5-creator/</a>  
演示: <a href="http://dev.filia.cn/S5-Creator/HTML/editor.htm" target="_blank" title="外部链接至 http://dev.filia.cn/S5-Creator/HTML/editor.htm" class="externalLink">http://dev.filia.cn/S5-Creator/HTML/editor.htm</a>  
该项目仍在开发中.

最近开发停滞不前了,除了时间上的原因, 另外就是技术上有一些问题. (编辑器器啊&#8230;&#8230;)  
决定先总结一下这个项目的架构,也给自己理一下思路.

整个S5Creator 我将它划分为如下几个组件.

  * 编辑器 编辑单张幻灯片
  * 缩略图 缩略图,同时用于不同幻灯片的切换
  * 主题选择器 选择主题
  * 工具列 提供对一些常用功能 (保存,新建,加载等)
  * 预览器 现在的实现是打开一个新的窗口
  * IO接口 配合不同的后端, 比如本地Javascript,PHP,Python

通过一个singleton 的 S5Creator对象来访问这些这些组件.

上面的前4个都是UI 组件, 我都是使用下面这样的思想来设计的.

  1. View 手工写的 HTML 和 CSS +基于模板生成的元素．(比如新建了一张幻灯片)
  2. Controller 通过CSS Selector 对元素附加事件处理函数, 同时这些CSS Selector 是可配置的.
  3. Model  使用 DOM元素本身,必要时添加定制的属性. (比如在按钮上添加action属性)  
    再比如选中选中某一个幻灯片时， 我直接在该幻灯片上添加一个CSS Class.  
    当然我也使用javascript对象来作模型，在不同组件之间传递时封装成一个slide对象。

代码说话, 下面缩略图视图组件的代码. (还有点乱, 不过基本成形了. 注意我使用了jquery 框架, 里面使用了一些jquery 扩展)  
// 还是不贴了, 太长了. 看这里吧. <a href="http://s5-creator.googlecode.com/svn/trunk/HTML/js/s5creator/thumbView.js" title="thumbView.js " target="_blank">thumbView.js</a>
