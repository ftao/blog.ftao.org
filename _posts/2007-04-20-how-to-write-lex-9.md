---
title: how to write lex (9)
author: Filia.Tao
layout: post
permalink: /2007/04/20/how-to-write-lex-9/
categories:
  - C/C++
  - compiler
  - lex
tags:
  - C/C++
  - compiler
  - lex
---
我今天把代码优化到处理c语言,用2秒钟.(原来需要10s).  
不过还是没法更flex 比,它的时间<1s.  
随便写写开发过程中的几点体会,可能和lex本身没有太大的关系.

  * 尽量不要用string . C++ 的string做的效率不高.
  * 不要使用magic number . 可以定义称const int 的就定义成const int.
  * 宏是万恶之源.如果一定要用,名字取长一点.
  * 用好typedef
  * 单元测试很重要.
  * 学会用工具分析性能.
  * 正则表达式很奇妙
  * 中文的编码是负的.