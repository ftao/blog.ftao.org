---
title: 'Missing Number &#8211; New version'
author: Filia.Tao
layout: post
permalink: /2007/09/12/missing-number-new-version/
categories:
  - 算法
  - 计算机科学
tags:
  - 算法
  - 计算机科学
---
今天开始做题,第一个题目, <a href="http://discuss.techinterview.org/default.asp?interview.11.539937.10" target="_blank">Missing Number &#8211; New version</a> .  
发现我竟然在很短的时间内就想到了思路. 题目摘录如下:

> An array A[1...n] contains all the integers from 0 to n except one. In this problem, we cannot access an entire integer in A with a single operation. The elements of A are represented in binary, and the only operation we can use to access them is “fetch the jth bit of A[i ]“, which takes constant time. Find the missing integer in O(n) time.

翻译成中文就是, 数组A[1..n] 包含 0-n 中的n-1个数, 只有一个不在里面. 数字以二进制存储,一次只能访问一位. (常数时间) . 现在要求在O(n)时间内找到不在其中的那个数.

下面是答案, 不想看答案的可以停止阅了.  
&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;

首先, 我们假设n =2<sup>k</sup> . (假设的合理性, 下面讨论) , 用k 位二进制数来表示一个数.  
很显然所有数中i-th 位 会有n/2 个1 ,n/2 个 0.

> <pre>init array T with 1..n
m = n-1
for i =1 to k
    for j = 1 to m
        B  =  []
        C  =  []
        if i-th-bit(A[j]) == 1
           Add j to B
        else
           Add j to C
    if size(B) &lt; size(C)
         T = B
    else
         T = C
    m = floor(m/2)

Now T should only have one element k,
toogle last bit of A[k] is the missing number .</pre>

算法分析:  
复杂度为 : n + n/2 + n/4 + n/8 + &#8230;&#8230;&#8230; + 2 + 1 < 2n = O(n)  
不过使用了一些多余的存储空间.  
另:n =2<sup>k</sup>假设的合理性.  
if 2<sup>k-1</sup> < n < 2<sup>k</sup> , 我们完全可以不管第一位, 因为它必定为1. (使用K位编码)  
所以　T(n) = T(2<sup>k-1</sup>)