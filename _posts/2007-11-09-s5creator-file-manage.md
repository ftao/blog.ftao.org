---
title: 'S5Creator &#8211; 文件管理'
author: Filia.Tao
layout: post
permalink: /2007/11/09/s5creator-file-manage/
categories:
  - javascript
tags:
  - javascript
  - jquery
  - S5Creator
  - table
---
昨天和今天把文件管理的功能从编辑页面移动到一个单独的页面.  
主要是新建/删除/浏览功能. 顺便把jQuery 的table (Sortable Table) UI组件来用了一下.  
演示: <a href="http://dev.filia.cn/S5-Creator/HTML/manage.html" target="_blank">http://dev.filia.cn/S5-Creator/HTML/manage.html</a>  
还有实现了一个全选/不选/反选的功能, 发现用jQuery 来做这个真是太简单了.

<div class="wp_syntax">
  <div class="code">
    <pre class="javascript" style="font-family:monospace;"><span style="color: #006600; font-style: italic;">//选择按钮</span>
&nbsp;
$<span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">this</span>._options.<span style="color: #660066;">selectButtonSelector</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">click</span><span style="color: #009900;">&#40;</span>
 <span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span>e<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#123;</span>
 	<span style="color: #003366; font-weight: bold;">var</span> which <span style="color: #339933;">=</span> $<span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">this</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">attr</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"which"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
 	<span style="color: #000066; font-weight: bold;">switch</span><span style="color: #009900;">&#40;</span>which<span style="color: #009900;">&#41;</span>
 	<span style="color: #009900;">&#123;</span>
 		<span style="color: #000066; font-weight: bold;">case</span> <span style="color: #3366CC;">'none'</span><span style="color: #339933;">:</span>
 			$<span style="color: #009900;">&#40;</span>fm._options.<span style="color: #660066;">checkboxSelector</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">removeAttr</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"checked"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
 			<span style="color: #000066; font-weight: bold;">break</span><span style="color: #339933;">;</span>
 		<span style="color: #000066; font-weight: bold;">case</span> <span style="color: #3366CC;">'all'</span><span style="color: #339933;">:</span>
 			$<span style="color: #009900;">&#40;</span>fm._options.<span style="color: #660066;">checkboxSelector</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">attr</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"checked"</span><span style="color: #339933;">,</span><span style="color: #3366CC;">"checked"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
 			<span style="color: #000066; font-weight: bold;">break</span><span style="color: #339933;">;</span>
 		<span style="color: #000066; font-weight: bold;">case</span> <span style="color: #3366CC;">'reverse'</span><span style="color: #339933;">:</span>
 			$<span style="color: #009900;">&#40;</span>fm._options.<span style="color: #660066;">checkboxSelector</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">each</span><span style="color: #009900;">&#40;</span><span style="color: #003366; font-weight: bold;">function</span><span style="color: #009900;">&#40;</span>i<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#123;</span>
 				<span style="color: #000066; font-weight: bold;">if</span><span style="color: #009900;">&#40;</span>$<span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">this</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">attr</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">'checked'</span><span style="color: #009900;">&#41;</span><span style="color: #009900;">&#41;</span>
 					$<span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">this</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">removeAttr</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">'checked'</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
 				<span style="color: #000066; font-weight: bold;">else</span>
 					$<span style="color: #009900;">&#40;</span><span style="color: #000066; font-weight: bold;">this</span><span style="color: #009900;">&#41;</span>.<span style="color: #660066;">attr</span><span style="color: #009900;">&#40;</span><span style="color: #3366CC;">"checked"</span><span style="color: #339933;">,</span><span style="color: #3366CC;">"checked"</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
 			<span style="color: #009900;">&#125;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
 			<span style="color: #000066; font-weight: bold;">break</span><span style="color: #339933;">;</span>
 	<span style="color: #009900;">&#125;</span>
 <span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span></pre>
  </div>
</div>

简单明白,逻辑清楚.  
其他更新:

  * 改进后端
  * 信息显示(上次保存时间,刚进行过的动作)
  * 代码清理
