---
title: how to write lex (6)
author: Filia.Tao
layout: post
permalink: /2007/04/14/how-to-write-lex-6/
categories:
  - compiler
  - lex
tags:
  - compiler
  - DFA
  - lex
  - NFA
  - ε-closure
---
NFA -> DFA  
算法是子集构造法.下面是龙书上关于这个算法的描述

> <pre>initially,ε-closure(s0) is the only state in Dstates and it is unmarked;</pre>
> 
> <pre>while there is an unmarked state T in Dstates do begin</pre>
> 
> <pre>mark T;</pre>
> 
> <pre>for each input symbol a do begin</pre>
> 
> <pre>U := ε-closure(move(T,a));</pre>
> 
> <pre>if U is not in Dstates then</pre>
> 
> <pre>add U as an unmarked state to Dstates</pre>
> 
> <pre>Dtran[T,a] : = U</pre>
> 
> <pre>end</pre>
> 
> <pre>end</pre>

几个很重要的概念:

  * ε-closure  就是当前状态通过ε边可以到达的所有状态的集合
  * 集合的ε-closure  就是集合中所有状态的ε-closure的并集
  * move(T,a) 就是T中所有状态通过a边可以到达的状态的集合

在实现上可以用FIFO队列来模拟Dstates, 那个 marked 不是必须的.  
每个状态的ε-closure 可以事先计算好,每次查表即可.  
对于转换后的DFA 初态就是ε-closure(s0) , 所有含有NFA终态的DFA状态为终态.  
算法中具体操作和使用数据结构有关.