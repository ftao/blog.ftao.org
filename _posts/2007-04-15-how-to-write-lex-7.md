---
title: how to write lex (7)
author: Filia.Tao
layout: post
permalink: /2007/04/15/how-to-write-lex-7/
categories:
  - compiler
  - lex
tags:
  - compiler
  - lex
---
以前的几篇文章并没有和我们的任务紧密的联系起来,忽略了很多东西.  
我们使用DFA来识别字符串,必须要知道输入字符串最终是匹配了lex 输入文件中哪个正则表达式,而不仅仅是是否匹配.  
所以我们需要引入新的数据结构,来表示NFA中每一个终态对应的哪一个正则表达式.  
我简单是使用一个MAP(哈希表)来表示这些信息.  
这些信息在对NFA进行操作时,需要进行相应的转换.但实际上需要转换的有下面几个地方.

  * 状态重新编号  
    改变MAP的key 即可
  * union  
    合并两个MAP即可.
  * NFA -> DFA  
    一个DFA状态对应一个NFA集合.  
    这个集合中可能若干个NFA终态,取他们对应正则表达式编号的最小值作为DFA状态对应的正则表达式编号.
  * DFA 最小化  
    类似上面的做法.一个min-DFA状态对应原来的DFA的多个状态.