---
title: how to write lex (5)
author: Filia.Tao
layout: post
permalink: /2007/04/14/how-to-write-lex-5/
categories:
  - compiler
  - lex
tags:
  - compiler
  - lex
---
NFA 的组合的实现.

  * closure A*  
    新产生一个状态newF(加入结束态集合),将原有的结束态通过ε边连接到newF.  
    通过ε边双向连接newF和初态.
  * option A?  
    和识别空字符串的NFA进行 union 即可
  * union A|B  
    合并两个NFA的状态集合,转换集合,终态集合.  
    取一个NFA的初态为初态, 并通过ε边连接到另一个NFA的初态
  * iteration A{min,max}  
    就如名字所示,迭代的方法就可以. 当然最终要调用concatenation
  * concatenation AB  
    合并状态集合和转化集合. A的初态为初态,B的终态为终态.  
    将A的终态通过ε边连接到B的初态

在这个过程中,我们会使用一些NFA常量,不如NULL\_FA和EMPTY\_STRING_FA.  
上面的描述转化成对数据结构的操作是很容易的.  
不过有一点需要注意,两个NFA中原来的状态编号可能是一样的.在合并过程需要进行转换 (比如将其中一个NFA的状态编号都加上一个常数).  
未完待续