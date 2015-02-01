---
title: how to write lex (3)
author: Filia.Tao
layout: post
permalink: /2007/04/13/how-to-write-lex-3/
categories:
  - compiler
  - lex
tags:
  - compiler
  - lex
---
#### 从正则表达式到NFA.

算法都是现成的,书上都有. 我采用的是 *Thompson&#8217;s construction *方法.

基本思想就是有简单的正则表达式的NFA,通过固定的规则来构造复杂的正则表达式的NFA.

  1. 构造简单的正则表达式的NFA. 我们可以称其为原子NFA.  
    比如识别一个字符的NFA. 识别类似[a-e0-9]这样的正则表达式的NFA.这两种NFA都只有两个状态.
  2. 实现各种NFA的组合操作 
      * closure   A*
      * option    A?
      * union     A|B
      * iteration  A{min,max}
      * concatenation   AB
  3. lex 支持的正则表达式可以从其POSIX规范中找到.  
    我们只实现一个子集, 砍掉的特性有</p> 
      * 超前查看
      * \x*digits *和\*digits *转义序列.
      * collation-related bracket symbols (这个东西我还没搞明白是什么.)
      * ^和$ 符号 匹配行首和行尾 (这个有点可惜)
    
    虽然砍掉了很多,但是简化后的正则表达式还是足够强大的. 起码处理C语言是没有问题的.</li> 
    
      * 分解一个复杂的正则表达式为简单的正则表达式的组合.  
        根据操作符的优先级采用递归下降的方法分析正则表达式.  
        这个可能要另外写一篇, 贴代码说事了.</ol> 
    
    未完待续.