---
title: 用JS操作Select
author: Filia.Tao
layout: post
permalink: /2007/10/27/tips-for-operator-on-select-using-js/
categories:
  - javascript
tags:
  - javascript
  - select
---
  1. 清空 <div class="wp_syntax">
      <div class="code">
        <pre class="javascript" style="font-family:monospace;">selectEle.<span style="color: #660066;">options</span>.<span style="color: #660066;">length</span> <span style="color: #339933;">=</span> <span style="color: #CC0000;"></span><span style="color: #339933;">;</span></pre>
      </div>
    </div>

  2. 添加 <div class="wp_syntax">
      <div class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #003366; font-weight: bold;">var</span> option <span style="color: #339933;">=</span> <span style="color: #003366; font-weight: bold;">new</span> Option<span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"text1"</span><span style="color: #339933;">,</span><span style="color: #3366CC;">"value1"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000066; font-weight: bold;">try</span><span style="color: #009900;">&#123;</span>
    selectEle.<span style="color: #660066;">add</span><span style="color: #009900;">&#40;</span>option<span style="color: #339933;">,</span><span style="color: #003366; font-weight: bold;">null</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span> <span style="color: #006600; font-style: italic;">// for firefox</span>
<span style="color: #009900;">&#125;</span><span style="color: #000066; font-weight: bold;">catch</span><span style="color: #009900;">&#40;</span>e<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#123;</span>
    selectEle.<span style="color: #660066;">add</span><span style="color: #009900;">&#40;</span>option<span style="color: #339933;">,-</span><span style="color: #CC0000;">1</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span> <span style="color: #006600; font-style: italic;">// for IE</span>
<span style="color: #009900;">&#125;</span></pre>
      </div>
    </div>
    
    也可以直接操作selectEle.options , 不过注意options 是一个伪数组(更arguments 一样), 不是一个数组.
    
    <div class="wp_syntax">
      <div class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #000066;">alert</span><span style="color: #009900;">&#40;</span>selectEle.<span style="color: #660066;">options</span> <span style="color: #000066; font-weight: bold;">instanceof</span> Array <span style="color: #339933;">===</span> <span style="color: #003366; font-weight: bold;">false</span><span style="color: #009900;">&#41;</span></pre>
      </div>
    </div>

  3. 获得选择的选项. (可以是 multiple 的) <div class="wp_syntax">
      <div class="code">
        <pre class="javascript" style="font-family:monospace;"><span style="color: #003366; font-weight: bold;">var</span> selected <span style="color: #339933;">=</span> <span style="color: #009900;">&#91;</span><span style="color: #009900;">&#93;</span><span style="color: #339933;">;</span>
<span style="color: #000066; font-weight: bold;">for</span><span style="color: #009900;">&#40;</span><span style="color: #003366; font-weight: bold;">var</span> i <span style="color: #339933;">=</span> <span style="color: #CC0000;"></span> <span style="color: #339933;">;</span> i <span style="color: #339933;">&</span>lt<span style="color: #339933;">;</span> selectEle.<span style="color: #660066;">options</span>.<span style="color: #660066;">length</span><span style="color: #339933;">;</span> i <span style="color: #339933;">++</span><span style="color: #009900;">&#41;</span>
<span style="color: #009900;">&#123;</span>
    <span style="color: #000066; font-weight: bold;">if</span><span style="color: #009900;">&#40;</span>selectEle.<span style="color: #660066;">options</span><span style="color: #009900;">&#91;</span>i<span style="color: #009900;">&#93;</span>.<span style="color: #660066;">selected</span><span style="color: #009900;">&#41;</span>
        selected.<span style="color: #660066;">push</span><span style="color: #009900;">&#40;</span>selectEle.<span style="color: #660066;">options</span><span style="color: #009900;">&#91;</span>i<span style="color: #009900;">&#93;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
<span style="color: #009900;">&#125;</span></pre>
      </div>
    </div>
