---
title: 关于　C++ iterator
author: Filia.Tao
layout: post
permalink: /2007/04/08/about-c-iterator/
categories:
  - C/C++
  - compiler
tags:
  - C++
  - C/C++
  - compiler
  - iterator
  - lex
  - STL
  - vector
---
这几天在写编译原理的课程设计。  
第一部分就是实现一个SEU-lex.  
花了将近一个礼拜的时间,基本把核心算法(RE->DFA->DFA->min DFA)给搞定了.  
C++ 好久不用,有点生疏了,遇到了很多问题.  
等把程序完全写完了,再好好整理整理一下.  
今天先说刚遇到的一个关于iterator问题.

我用一个iterator 访问一个vector, 同时有往vector里面加入新的元素.  
但iterator迭代到新加入的元素是会出现错误.(类似空指针)  
看来有空还要好好看看STL的书,把这些个概念搞清楚.
