---
title: how to write lex (2)
author: Filia.Tao
layout: post
permalink: /2007/04/13/how-to-write-lex-2/
categories:
  - compiler
  - lex
tags:
  - compiler
  - lex
---
怎样表示NFA? 也就是数据结构的设计问题.

> A *nondeterministic finite autormaton *(NFA for short) is a mathematical model that consists of
> 
>   1. a set of states S
>   2. a set of input sysmbols Σ (the *input symbol alphabet*)
>   3. a transition function *move* that maps state-symbol pair to sets of states
>   4. a state s0 that is distinguished as the *start *(or *initial*) state
>   5. a sets of states F distinguished as *accepting *(or *final*) states

以上文字来自<a href="http://www.douban.com/subject/1417281/" title="龙书" target="_blank">龙书</a> . 有很多种方法可以实现上面的模型.  
我采用了一种最直接的方法,可能不是效率最高的方法.但是这种方法容易理解,思路清晰.

我设计的NFA类主要的数据成员有

  * 状态集合 states
  * 开始状态 initialState
  * 终止状态集合 finalStates
  * 转换集合 transitions ,集合的元素是 (from,to,lable)的三元组
  * alphabet 我默认其为ASCII字符.

这个类显然可以实现上面的模型.move函数可以通过查询transitions 来实现.  
当然这样每次查询都遍历这个列表是效率很低的.  
我建立了三个map 来将from,to,label映射为对应的transitions 编号集合.  
DFA是一个NFA的特例. 我们可以使用继承关系来实现. 不过我没有采用这种方法. 而是简单的在类里面加了一个 _isDeterminized 来表示是否确定化了.  
这种方法我还用在其他很多地方. 不如 _isMinimized 等等.

未完待续.

<a href="http://www.douban.com/subject/1417281/" title="龙书" target="_blank"></a>
