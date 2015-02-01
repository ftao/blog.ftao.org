---
title: how to write lex (4)
author: Filia.Tao
layout: post
permalink: /2007/04/14/how-to-write-lex-4/
categories:
  - compiler
  - lex
tags:
  - compiler
  - lex
---
用递归下降的方法分析正则表达式.  
正则表达式的语法可以用下面的产生式来表示:

  * E -> S&#8217;|&#8217; E | S
  * S -> IS | I
  * I -> GT |G|N
  * G -> (E)
  * G -> [R]
  * G -> C
  * N -> “M”
  * M -> CM | C
  * T -> *|?|+|{a,b}|{a}|{a,}|{,b}|ε

说明:

  * E 一个正则表达式(Expression)
  * S 一个序列( Sequences)
  * I 一个项目 (Item)
  * G 一个项目的前缀部分
  * T 一个项目的后缀部分 说明重复多次
  * R 范围 (Range) []中间的部分
  * C 单个字符
  * N 一个字符串

最后几个我没有再展开,写出完整的定义了. 写这个产生式仅仅是为了说明问题.可能并不严密和完整.
