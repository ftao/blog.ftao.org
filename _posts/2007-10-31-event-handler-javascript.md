---
title: '事件处理 &#8211; javascript'
author: Filia.Tao
layout: post
permalink: /2007/10/31/event-handler-javascript/
categories:
  - javascript
tags:
  - event
  - javascript
---
  1. Basic Event Handling 
      * 定义为HTML属性 :　一串javascript 代码  
        作用域链很复杂.
      * 作为javascript属性的事件处理器: 普通javascript函数  
        作用域链比较简单. (调用对象,全局对象)
      * 特殊事件-计时器事件  
        setTimeout() 和setInterval()
      * 特殊事件-错误事件  
        window.onerror . 从来不用这个, 我觉得也不应该用.
  2. Event Object  
    首先 在事件处理函数中一般用event || window.event 来获得事件对象.  
    常用的属性有下面几个</p> 
      * target / srcElement 发生事件的DOM元素.
      * preventDefault 取消默认事件 (IE 设置 returnValue为false,来取消)
      * stopPropagation / cancelBubble 取消传播.
      * <span class="docPubcolor"><span class="docPubcolor"><span class="docMonofont">relatedTarget</span></span></span> mouseout/mouseover 事件才会用 . (没用过:))
      * which 鼠标事件 :(1,2,3 表示左中右) 键盘事件:按键编码  
        IE对两者分别是 button 和keyCode
      * ctrlKey , altKey, shiftKey, metaKey 修饰键是否按下
      * pageX/pageY 事件发生的位置. 相对于窗口的左上角.
下面是jquery 中event中的fix函数. 处理不同浏览器中的event对象的不同点.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
</pre>
      </td>
      
      <td class="code">
        <pre class="javascript" style="font-family:monospace;">fix<span style="color: #339933;">:</span> <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span>event<span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
    <span style="color: #006600; font-style: italic;">// store a copy of the original event object</span>
    <span style="color: #006600; font-style: italic;">// and clone to set read-only properties</span>
    <span style="color: #003366; font-weight: bold;">var</span> originalEvent <span style="color: #339933;">=</span> event<span style="color: #339933;">;</span>
    event <span style="color: #339933;">=</span> jQuery.<span style="color: #660066;">extend</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#123;</span><span style="color: #009900;">&#125;</span><span style="color: #339933;">,</span> originalEvent<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
    <span style="color: #006600; font-style: italic;">// add preventDefault and stopPropagation since</span>
    <span style="color: #006600; font-style: italic;">// they will not work on the clone</span>
    event.<span style="color: #660066;">preventDefault</span> <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
        <span style="color: #006600; font-style: italic;">// if preventDefault exists run it on the original event</span>
        <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>originalEvent.<span style="color: #660066;">preventDefault</span><span style="color: #009900;">&#41;</span>
            originalEvent.<span style="color: #660066;">preventDefault</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
        <span style="color: #006600; font-style: italic;">// otherwise set the returnValue property of the original event to false (IE)</span>
        originalEvent.<span style="color: #660066;">returnValue</span> <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">false</span><span style="color: #339933;">;</span>
    <span style="color: #009900;">&#125;</span><span style="color: #339933;">;</span>
    event.<span style="color: #660066;">stopPropagation</span> <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
        <span style="color: #006600; font-style: italic;">// if stopPropagation exists run it on the original event</span>
        <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>originalEvent.<span style="color: #660066;">stopPropagation</span><span style="color: #009900;">&#41;</span>
            originalEvent.<span style="color: #660066;">stopPropagation</span><span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
        <span style="color: #006600; font-style: italic;">// otherwise set the cancelBubble property of the original event to true (IE)</span>
        originalEvent.<span style="color: #660066;">cancelBubble</span> <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">true</span><span style="color: #339933;">;</span>
    <span style="color: #009900;">&#125;</span><span style="color: #339933;">;</span>
&nbsp;
    <span style="color: #006600; font-style: italic;">// Fix target property, if necessary</span>
    <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span> <span style="color: #339933;">!</span>event.<span style="color: #660066;">target</span> <span style="color: #339933;">&</span>amp<span style="color: #339933;">;&</span>amp<span style="color: #339933;">;</span> event.<span style="color: #660066;">srcElement</span> <span style="color: #009900;">&#41;</span>
        event.<span style="color: #660066;">target</span> <span style="color: #339933;">=</span> event.<span style="color: #660066;">srcElement</span><span style="color: #339933;">;</span>
&nbsp;
    <span style="color: #006600; font-style: italic;">// check if target is a textnode (safari)</span>
    <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span>jQuery.<span style="color: #660066;">browser</span>.<span style="color: #660066;">safari</span> <span style="color: #339933;">&</span>amp<span style="color: #339933;">;&</span>amp<span style="color: #339933;">;</span> event.<span style="color: #660066;">target</span>.<span style="color: #660066;">nodeType</span> <span style="color: #339933;">==</span> <span style="color: #CC0000;">3</span><span style="color: #009900;">&#41;</span>
        event.<span style="color: #660066;">target</span> <span style="color: #339933;">=</span> originalEvent.<span style="color: #660066;">target</span>.<span style="color: #660066;">parentNode</span><span style="color: #339933;">;</span>
&nbsp;
    <span style="color: #006600; font-style: italic;">// Add relatedTarget, if necessary</span>
    <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span> <span style="color: #339933;">!</span>event.<span style="color: #660066;">relatedTarget</span> <span style="color: #339933;">&</span>amp<span style="color: #339933;">;&</span>amp<span style="color: #339933;">;</span> event.<span style="color: #660066;">fromElement</span> <span style="color: #009900;">&#41;</span>
        event.<span style="color: #660066;">relatedTarget</span> <span style="color: #339933;">=</span> event.<span style="color: #660066;">fromElement</span> <span style="color: #339933;">==</span> event.<span style="color: #660066;">target</span> <span style="color: #339933;">?</span> event.<span style="color: #660066;">toElement</span> <span style="color: #339933;">:</span> event.<span style="color: #660066;">fromElement</span><span style="color: #339933;">;</span>
&nbsp;
    <span style="color: #006600; font-style: italic;">// Calculate pageX/Y if missing and clientX/Y available</span>
    <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span> event.<span style="color: #660066;">pageX</span> <span style="color: #339933;">==</span> <span style="color: #003366; font-weight: bold;">null</span> <span style="color: #339933;">&</span>amp<span style="color: #339933;">;&</span>amp<span style="color: #339933;">;</span> event.<span style="color: #660066;">clientX</span> <span style="color: #339933;">!=</span> <span style="color: #003366; font-weight: bold;">null</span> <span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#123;</span>
        <span style="color: #003366; font-weight: bold;">var</span> e <span style="color: #339933;">=</span> document.<span style="color: #660066;">documentElement</span><span style="color: #339933;">,</span> b <span style="color: #339933;">=</span> document.<span style="color: #660066;">body</span><span style="color: #339933;">;</span>
        event.<span style="color: #660066;">pageX</span> <span style="color: #339933;">=</span> event.<span style="color: #660066;">clientX</span> <span style="color: #339933;">+</span> <span style="color: #009900;">&#40;</span>e <span style="color: #339933;">&</span>amp<span style="color: #339933;">;&</span>amp<span style="color: #339933;">;</span> e.<span style="color: #660066;">scrollLeft</span> <span style="color: #339933;">||</span> b.<span style="color: #660066;">scrollLeft</span> <span style="color: #339933;">||</span> <span style="color: #CC0000;"></span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
        event.<span style="color: #660066;">pageY</span> <span style="color: #339933;">=</span> event.<span style="color: #660066;">clientY</span> <span style="color: #339933;">+</span> <span style="color: #009900;">&#40;</span>e <span style="color: #339933;">&</span>amp<span style="color: #339933;">;&</span>amp<span style="color: #339933;">;</span> e.<span style="color: #660066;">scrollTop</span> <span style="color: #339933;">||</span> b.<span style="color: #660066;">scrollTop</span> <span style="color: #339933;">||</span> <span style="color: #CC0000;"></span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
    <span style="color: #009900;">&#125;</span>
&nbsp;
    <span style="color: #006600; font-style: italic;">// Add which for key events</span>
    <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span> <span style="color: #339933;">!</span>event.<span style="color: #660066;">which</span> <span style="color: #339933;">&</span>amp<span style="color: #339933;">;&</span>amp<span style="color: #339933;">;</span> <span style="color: #009900;">&#40;</span>event.<span style="color: #660066;">charCode</span> <span style="color: #339933;">||</span> event.<span style="color: #660066;">keyCode</span><span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#41;</span>
        event.<span style="color: #660066;">which</span> <span style="color: #339933;">=</span> event.<span style="color: #660066;">charCode</span> <span style="color: #339933;">||</span> event.<span style="color: #660066;">keyCode</span><span style="color: #339933;">;</span>
&nbsp;
    <span style="color: #006600; font-style: italic;">// Add metaKey to non-Mac browsers (use ctrl for PC's and Meta for Macs)</span>
    <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span> <span style="color: #339933;">!</span>event.<span style="color: #660066;">metaKey</span> <span style="color: #339933;">&</span>amp<span style="color: #339933;">;&</span>amp<span style="color: #339933;">;</span> event.<span style="color: #660066;">ctrlKey</span> <span style="color: #009900;">&#41;</span>
        event.<span style="color: #660066;">metaKey</span> <span style="color: #339933;">=</span> event.<span style="color: #660066;">ctrlKey</span><span style="color: #339933;">;</span>
&nbsp;
    <span style="color: #006600; font-style: italic;">// Add which for click: 1 == left; 2 == middle; 3 == right</span>
    <span style="color: #006600; font-style: italic;">// Note: button is not normalized, so don't use it</span>
    <span style="color: #000066; font-weight: bold;">if</span> <span style="color: #009900;">&#40;</span> <span style="color: #339933;">!</span>event.<span style="color: #660066;">which</span> <span style="color: #339933;">&</span>amp<span style="color: #339933;">;&</span>amp<span style="color: #339933;">;</span> event.<span style="color: #660066;">button</span> <span style="color: #009900;">&#41;</span>
        event.<span style="color: #660066;">which</span> <span style="color: #339933;">=</span> <span style="color: #009900;">&#40;</span>event.<span style="color: #660066;">button</span> <span style="color: #339933;">&</span>amp<span style="color: #339933;">;</span> <span style="color: #CC0000;">1</span> <span style="color: #339933;">?</span> <span style="color: #CC0000;">1</span> <span style="color: #339933;">:</span> <span style="color: #009900;">&#40;</span> event.<span style="color: #660066;">button</span> <span style="color: #339933;">&</span>amp<span style="color: #339933;">;</span> <span style="color: #CC0000;">2</span> <span style="color: #339933;">?</span> <span style="color: #CC0000;">3</span> <span style="color: #339933;">:</span> <span style="color: #009900;">&#40;</span> event.<span style="color: #660066;">button</span> <span style="color: #339933;">&</span>amp<span style="color: #339933;">;</span> <span style="color: #CC0000;">4</span> <span style="color: #339933;">?</span> <span style="color: #CC0000;">2</span> <span style="color: #339933;">:</span> <span style="color: #CC0000;"></span> <span style="color: #009900;">&#41;</span> <span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
    <span style="color: #000066; font-weight: bold;">return</span> event<span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span></pre>
      </td>
    </tr>
  </table>
</div>

  3. 添加删除/事件  
    IE: ele.attachEvent(“onxxx”,xxx_handler)  
    ele.detachEvent(“onxxx”,xxx_handler)  
    The Level 2 DOM Events :  
    ele.addEventListener(“xxx&#8217;,xxx\_handler,is\_capture)  
    ele.removeEventListener(“xxx&#8217;,xxx\_handler,is\_capture)
  4. 对事件的支持浏览器差异很大. 不过已经有很多现成的库, 为我们封装了起来. 能用库就用库吧.