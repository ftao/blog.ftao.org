---
title: Find the Array
author: Filia.Tao
layout: post
permalink: /2007/09/13/find-the-array/
categories:
  - 算法
tags:
  - 算法
---
接着做题.原题在这里 <a href="http://discuss.techinterview.org/default.asp?interview.11.541329.2" target="_blank">Find the Array.</a>

草草的翻译一下就是

> 有一个原始数组o[1..n], 1..n的一个排列. 另有一个数组, g[1..n] , g[i] 表示在o[i+1..n] 中比 o[i] 小的数的数目. 给定g 求 o .

问题上挺简单的.我的解答如下:

> <pre>the given array g[1..n]
the original  array o[1..n]

let i = the index of first 0 in g then o[i] = 1
for j =1 to i
     g[i] = g[i] -1
again , let i = the index of first 0 in g , then o[i] = 2

repeat the steps utils find all the element.</pre>

我的这个算法时间复杂度为 2n + 2(n -1) + 2(n -2)+ 2(n-3) + &#8230;.. 2 \* 1 =O(n\**2)  
**很不好.. **网站是说有nlogn 的方法, 我想不出来&#8230;.  
这个算法实际上和counting sort 有一些类似.  
更抽象一点的话, 都是用某一种数据结构来保存了次序信息. (昨天BBS 上有人讨论胜者树, 也是一种保存次序信息的数据结构).
