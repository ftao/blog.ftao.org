---
title: 编译作业 递归下降语法分析
author: Filia.Tao
layout: post
permalink: /2007/01/20/recurisive-descent-parsing/
categories:
  - compiler
  - javascript
  - python
tags:
  - compiler
  - javascript
  - python
---
编译作业 递归下降语法分析

语法很简单,就是只含+,*,(,) 的算术表达式:

<pre>E->E+F|F</pre>

<pre>E->F*G|G</pre>

<pre>G->(E)|NUM</pre>

用Python写了一个,很粗糙,但是能用。

<!--more-->

lex.py  
[CODE::python/complier/lex.py::Python/]

parse.py  
[CODE::python/complier/parse.py::Python/]