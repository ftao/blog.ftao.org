---
title: 怎样用py2exe
author: Filia.Tao
layout: post
permalink: /2007/01/23/how-to-use-py2exe/
categories:
  - python
tags:
  - py2exe
  - python
---
刚把编译课的实验完成,用Python写的. 估计可能肯定老师是没有装Python的,所以得把py编译成exe.搬出py2exe. 摸索了一会,原来是这样用的.

建一个setup.py

[CODE::python/complier/setup.py::Python/]

在运行下面的命令,

` python setup.py py2exe `

就可以了.

几个问题.  
我实际上没有用到ast.exe, ast.py只是lrparse.py 用到了这个文件.  
但是如果列表里不加上ast.py,生成lrparse.exe 无法运行.  
需要把ast.py 拷贝过去.
