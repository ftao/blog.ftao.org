---
title: How to write lex (1)
author: Filia.Tao
layout: post
permalink: /2007/04/12/how-to-write-lex-1/
categories:
  - compiler
  - lex
tags:
  - compiler
  - lex
---
这几天在写编译原理的课程设计,第一部分就是写一个SEU Lex. 难度不小. 现在核心部分已经完成了. 整理一下,写个教程. 希望对一些人有用.

lex 是词法分析器的生成器.  
输入为lex定义问题( .l文件 ).  
输出为词法分析器的C代码.  
lex有POSIX 标准  
<a href="http://www.opengroup.org/onlinepubs/009695399/utilities/lex.html" title="lex Posix 标准" target="_blank">http://www.opengroup.org/onlinepubs/009695399/utilities/lex.html<br /> </a>

一个完整lex的应该有三部分:

  1. lex定义 文件解析
  2. 正则表达式到NFA, NFA -> DFA, DFA 最小化算法的实现.
  3. 输出的词法分析器C代码模板

这里面工作量最大是第二部分,虽然算法都是现成的,自己实现还是要花一些时间和精力的.

未完待续.