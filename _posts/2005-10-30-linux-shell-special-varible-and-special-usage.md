---
title: '从编程语言的观点看linux shell&#8212;特殊的变量和变量的特殊用法'
author: Filia.Tao
layout: post
permalink: /2005/10/30/linux-shell-special-varible-and-special-usage/
categories:
  - Linux
---
<div>
  以BASH 为讨论的shell 语言以下就是一些特殊变量<br /> $ SHELL 的PID (由来标识进程的一个数)<br /> - 当前sh的选项<br /> ? 上一个命令的退出状态值 </p> 
  
  <p>
    ! 最后一个放入后台作业的PID值<br /> 比较有用的应该算? 和 !，注意引用这些变量的值同样需要用$。($$,$-,$?,$!)
  </p>
  
  <p>
    再谈谈所谓的变量修改符。<br /> shell中有一些特殊的操作符可以检验和修改变量。
  </p>
  
  <p>
    我觉的这些操作符，其实并不怎么重要.<br /> 我用C++的?:操作符来说明一下。<br /> ${var:-value} 等价于 var?var:value;<br /> ${var:+value} 等价于 var?var:(var=value)
  </p>
  
  <p>
    ${var:+value} 等价于 var?value:NULL<br /> ${var:?value}的作用是当var没有设置或为NULL是将var的名字和value作为这个表达式的值。常用于自定义错误输出。<br /> $ echo ${namex:?”namex is undefined”}
  </p>
  
  <p>
    namex: namex is undefined<br /> 如果你用如下命令，得到的是系统的错误输出。<br /> $ echo ${namex:?}<br /> namex : parameter null or not set
  </p>
  
  <p>
    这里:-的作用是如果name设置了值而不为NULL,这个值就是jack否则就是mike还有两个比较容易理解的操作。<br /> ${val:offset} 从offset开始提取子字符串<br /> ${val:offset:length} 从offset开始提取length长子字符串
  </p>
</div>

<img height="1" src="http://c.services.spaces.live.com/CollectionWebService/c.gif?space=why-gudu&#038;page=RSS%3a+%e4%bb%8e%e7%bc%96%e7%a8%8b%e8%af%ad%e8%a8%80%e7%9a%84%e8%a7%82%e7%82%b9%e7%9c%8blinux+shell---%e7%89%b9%e6%ae%8a%e7%9a%84%e5%8f%98%e9%87%8f%e5%92%8c%e5%8f%98%e9%87%8f%e7%9a%84%e7%89%b9%e6%ae%8a%e7%94%a8%e6%b3%95&#038;referrer=" width="1" border="0" /><img height="1" src="http://c.live.com/c.gif?NC=31263&#038;NA=1149&#038;PI=73329&#038;RF=&#038;DI=3919&#038;PS=85545&#038;TP=why-gudu.spaces.live.com&#038;GT1=why-gudu%3b2052" width="1" />