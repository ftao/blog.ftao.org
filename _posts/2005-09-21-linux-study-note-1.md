---
title: linux 学习笔记 1
author: Filia.Tao
layout: post
permalink: /2005/09/21/linux-study-note-1/
categories:
  - Linux
---
<div>
  新学期开始，刚过的短学期忙着写C++课程设计，新学期虽然课不少但课外要作的事少多了。<br /> 最近在linux公社上看了几个帖子，决定开始认真学习linux。<br /> 先从shell&#8211;bash开始。<br /> 最近从网络上下载的大量图片，文件名的格式为ID_{Date}_{A NUMBER}_{INDEX}_ </p> 
  
  <p>
    {SOMETHING ELSE} (不含有{ 和｝)。决定写一个脚本整理一下。<br /> 按{Date}_{A NUMBER} 整理到不同的目录下。(这样的文件往往是从同一个的地方下的)<br /> 一开始准备通过循环，在循环中，先用find命令得到第一文件然后将类似的文件移动到对
  </p>
  
  <p>
    应的文件夹中。最终将所有文件处理完闭。但是就是没有办法使find命令只返回第一个文
  </p>
  
  <p>
    件，或从中截取到第一个文件名。<br /> 接着找到了awk这个强大的工具。不过我发现awk后面的{action}，似乎不能是系统命令。<br /> 最后采取了如下的方法<br /> <font color="#0000cc"><font color="#99ff99">ls -1 |awk -F_ &#8216;/^ID/{print “if [ -d ""$2"_"$3"" ]; then “;print “echo </font></font>
  </p>
  
  <p>
    <font color="#0000cc"><font color="#99ff99">“”";print “else”;print “mkdir “$2&#8243;_”$3;print “mv “$1&#8243;_”$2&#8243;_”$3&#8243;* </font></font>
  </p>
  
  <p>
    <font color="#0000cc"><font color="#99ff99">“$2&#8243;_”$3;print “fi”;}&#8217;>go<br /> /bin/bash go<br /> rm go</font><br /> </font>在awk中形成一系列命令序列，重定向到一个文件中，再从文件中执行命令，完成操作。</div> 
    
    <p>
      <img height="1" src="http://c.services.spaces.live.com/CollectionWebService/c.gif?space=why-gudu&#038;page=RSS%3a+linux+%e5%ad%a6%e4%b9%a0%e7%ac%94%e8%ae%b0+1&#038;referrer=" width="1" border="0" /><img height="1" src="http://c.live.com/c.gif?NC=31263&#038;NA=1149&#038;PI=73329&#038;RF=&#038;DI=3919&#038;PS=85545&#038;TP=why-gudu.spaces.live.com&#038;GT1=why-gudu%3b2052" width="1" />
    </p>