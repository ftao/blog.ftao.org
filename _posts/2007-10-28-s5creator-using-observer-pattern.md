---
title: 'S5Creator &#8211; 使用Observer 模式重构'
author: Filia.Tao
layout: post
permalink: /2007/10/28/s5creator-using-observer-pattern/
categories:
  - javascript
tags:
  - design-pattern
  - javascript
  - Observer
  - S5Creator
  - 设计模式
  - 重构
---
今天使用Observer 重构了S5Creator. 主要是将各个模块之间解耦.  
首先快速的实现一个观察者模式. (现在暂时放在 S5Creator 这个类上, 以后再重构..)

<div class="wp_syntax">
  <div class="code">
    <pre class="javascript" style="font-family:monospace;">S5Creator.<span style="color: #660066;">prototype</span>.<span style="color: #660066;">register</span> <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span>topic<span style="color: #339933;">,</span>observer<span style="color: #009900;">&#41;</span>
<span style="color: #009900;">&#123;</span>
	<span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #339933;">!</span><span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">this</span>._topics<span style="color: #009900;">&#91;</span>topic<span style="color: #009900;">&#93;</span> <span style="color: #000066; font-weight: bold;">instanceof</span> Array<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span>
		<span style="color: #000066; font-weight: bold;">this</span>._topics<span style="color: #009900;">&#91;</span>topic<span style="color: #009900;">&#93;</span> <span style="color: #339933;">=</span> <span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">;</span>
	<span style="color: #000066; font-weight: bold;">this</span>._topics<span style="color: #009900;">&#91;</span>topic<span style="color: #009900;">&#93;</span>.<span style="color: #660066;">push</span><span style="color: #009900;">&#40;</span>observer<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span>
&nbsp;
S5Creator.<span style="color: #660066;">prototype</span>.<span style="color: #660066;">notify</span> <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span>topic<span style="color: #339933;">,</span>data<span style="color: #009900;">&#41;</span>
<span style="color: #009900;">&#123;</span>
	console.<span style="color: #660066;">log</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"notify "</span> <span style="color: #339933;">+</span> topic<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
	<span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span><span style="color: #339933;">!</span><span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">this</span>._topics<span style="color: #009900;">&#91;</span>topic<span style="color: #009900;">&#93;</span> <span style="color: #000066; font-weight: bold;">instanceof</span> Array<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span>
		<span style="color: #000066; font-weight: bold;">return</span> <span style="color: #339933;">;</span>
	<span style="color: #000066; font-weight: bold;">this</span>._topics<span style="color: #009900;">&#91;</span>topic<span style="color: #009900;">&#93;</span>.<span style="color: #660066;">each</span><span style="color: #009900;">&#40;</span><span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span>observer<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#123;</span>
		observer.<span style="color: #660066;">call</span><span style="color: #009900;">&#40;</span><span style="color: #003366; font-weight: bold;">null</span><span style="color: #339933;">,</span>data<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
	<span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span>
<span style="color: #009900;">&#125;</span></pre>
  </div>
</div>

很简单的一段代码.  
有下面的一个场景, 我们有一个主题选择器让我们选择主题. 选择完成以后我们需要通知编辑器更新主题.  
以前我们需要由ThemeSelector 显示的调用Editor 的setContentCss.  
现在我们只需要这样

<div class="wp_syntax">
  <div class="code">
    <pre class="javascript" style="font-family:monospace;">Editor.<span style="color: #660066;">prototype</span>.<span style="color: #660066;">init</span> <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>
<span style="color: #009900;">&#123;</span>
	<span style="color: #003366; font-weight: bold;">var</span> editor <span style="color: #339933;">=</span> <span style="color: #000066; font-weight: bold;">this</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #006600; font-style: italic;">//注册主题改变事件</span>
	S5Creator.<span style="color: #660066;">singleton</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">register</span><span style="color: #009900;">&#40;</span>
		<span style="color: #3366CC;">"theme_change"</span><span style="color: #339933;">,</span>
		<span style="color: #000066; font-weight: bold;">this</span>.<span style="color: #660066;">setContentCss</span>.<span style="color: #660066;">bind</span><span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">this</span><span style="color: #009900;">&#41;</span>
	<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
        <span style="color: #006600; font-style: italic;">// 其他代码</span>
<span style="color: #009900;">&#125;</span></pre>
  </div>
</div>

和这样

<div class="wp_syntax">
  <div class="code">
    <pre class="javascript" style="font-family:monospace;">ThemeSelector.<span style="color: #660066;">prototype</span>.<span style="color: #660066;">select</span> <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span>theme<span style="color: #009900;">&#41;</span>
<span style="color: #009900;">&#123;</span>
	<span style="color: #000066; font-weight: bold;">if</span><span style="color: #009900;">&#40;</span>theme<span style="color: #009900;">&#41;</span>
	<span style="color: #009900;">&#123;</span>
		<span style="color: #003366; font-weight: bold;">var</span> path <span style="color: #339933;">=</span> <span style="color: #000066; font-weight: bold;">this</span>._options.<span style="color: #660066;">themePath</span> <span style="color: #339933;">+</span> <span style="color: #3366CC;">'/'</span>
			 <span style="color: #339933;">+</span> $<span style="color: #009900;">&#40;</span>theme<span style="color: #009900;">&#41;</span>.<span style="color: #660066;">attr</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">'theme'</span><span style="color: #009900;">&#41;</span> <span style="color: #339933;">+</span> <span style="color: #3366CC;">'/'</span> <span style="color: #339933;">+</span> <span style="color: #000066; font-weight: bold;">this</span>._options.<span style="color: #660066;">editorCssName</span><span style="color: #339933;">;</span>
		<span style="color: #006600; font-style: italic;">//注意这里</span>
		S5Creator.<span style="color: #660066;">singleton</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">notify</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"theme_change"</span><span style="color: #339933;">,</span>path<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		$<span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">this</span>._box<span style="color: #009900;">&#41;</span>.<span style="color: #660066;">dialogClose</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
	<span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span></pre>
  </div>
</div>

解释一下上面有一个bind. 我很喜欢这个东西. 可以让我不用总是担心*this*.

<div class="wp_syntax">
  <div class="code">
    <pre class="javascript" style="font-family:monospace;"><span style="color: #003366; font-weight: bold;">Function</span>.<span style="color: #660066;">prototype</span>.<span style="color: #660066;">bind</span> <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span>obj<span style="color: #009900;">&#41;</span>
<span style="color: #009900;">&#123;</span>
	<span style="color: #003366; font-weight: bold;">var</span> func <span style="color: #339933;">=</span> <span style="color: #000066; font-weight: bold;">this</span><span style="color: #339933;">;</span>
	<span style="color: #000066; font-weight: bold;">return</span> <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#123;</span>
		func.<span style="color: #660066;">apply</span><span style="color: #009900;">&#40;</span>obj<span style="color: #339933;">,</span>arguments<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
	<span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span></pre>
  </div>
</div>

我还将Observer 模式用在了其他几个地方. 比如编辑器内容更新后,通知缩略图. 改变了正在编辑的slide 以后, 通知编辑器更新内容.
