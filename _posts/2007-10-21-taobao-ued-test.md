---
title: 淘宝UED在线测试
author: Filia.Tao
layout: post
permalink: /2007/10/21/taobao-ued-test/
categories:
  - javascript
tags:
  - array
  - indexOf
  - javascript
  - ued
---
今天去做了一下淘宝UED 招聘的在线测试题目. http://ued.taobao.com/job/ceil.php.  
就三个题目,号称要1个小时宝贵的时间.  
不过发现我框架用多了, javascript 语言本身的忘了不少.  
Firefox 用多了, IE 就忘了.  
有个题目, 我竟然用了 Array.indexOf . 这个标准里面没有, 不过在javascript shell ( <a href="http://www.squarefree.com/bookmarklets/webdevel.html" target="_blank">见这里</a>)里面一试,竟然可以. 提交了才想起来还没在IE 中测试.

下面手工实现一下这个函数, 其实很简单.

<div class="wp_syntax">
  <div class="code">
    <pre class="javascript" style="font-family:monospace;">Array.<span style="color: #660066;">prototype</span>.<span style="color: #660066;">indexOf</span> <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span>x<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#123;</span>
    <span style="color: #000066; font-weight: bold;">for</span><span style="color: #009900;">&#40;</span><span style="color: #003366; font-weight: bold;">var</span> i <span style="color: #339933;">=</span> <span style="color: #CC0000;"></span> <span style="color: #339933;">;</span> i <span style="color: #339933;">&lt;</span> <span style="color: #000066; font-weight: bold;">this</span>.<span style="color: #660066;">length</span><span style="color: #339933;">;</span> i<span style="color: #339933;">++</span><span style="color: #009900;">&#41;</span>
    <span style="color: #009900;">&#123;</span>
        <span style="color: #000066; font-weight: bold;">if</span><span style="color: #009900;">&#40;</span>x <span style="color: #339933;">===</span> <span style="color: #000066; font-weight: bold;">this</span><span style="color: #009900;">&#91;</span>i<span style="color: #009900;">&#93;</span><span style="color: #009900;">&#41;</span> <span style="color: #000066; font-weight: bold;">return</span> i<span style="color: #339933;">;</span>
    <span style="color: #009900;">&#125;</span>
    <span style="color: #000066; font-weight: bold;">return</span> <span style="color: #339933;">-</span><span style="color: #CC0000;">1</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span></pre>
  </div>
</div>