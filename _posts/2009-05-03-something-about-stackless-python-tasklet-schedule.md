---
title: stackless python 中tasklet 调度的心得
author: Filia.Tao
layout: post
permalink: /2009/05/03/something-about-stackless-python-tasklet-schedule/
categories:
  - python
tags:
  - python
  - schedule
  - stackless
---
*[如果你不知道什么是stackless python , 请先阅读<a href="http://l.99081.com/lych/stackless.html" target="_blank"> Stackless Python 并发式编程介绍</a>]*

在stackless python 中两种方式可以交出控制权以让其他tasklet 运行.

  * stackless.schedule()  
    当调用 stackless.schedule() 的时候，当前活动微进程将暂停执行，并将自身重新插入到调度器队列的末尾，好让下一个微进程被执行。一旦在它前面的所有其他微进程都运行过了，它将从上次停止的地方继续开始运行。
  * channel.send() or channel.receive()  
    接收的微进程调用 channel.receive() 的时候，便阻塞住，这意味着该微进程暂停执行，直到有信息从这个通道送过来。除了往这个通道发送信息以外，没有其他任何方式可以让这个微进程恢复运行。  
    若有其他微进程向这个通道发送了信息，则不管当前的调度到了哪里，这个接收的微进程都立即恢复执行；而发送信息的微进程则被转移到调度列表的末尾，就像调用了 stackless.schedule() 一样。

上面两端都是从上面提到的文档中摘录过来.  都写得很清楚。 但是要真正的理解这个模型， 我还是走了不少弯路的.下面介绍一点经验和心得. 为了说明问题, 我写了一些测试代码, 原型是我最近在写的一个爬虫的框架.  代码放在了 <http://code.filia.cn/python/stackless/>.

  * 如果你所有的tasklet 都是用stackless.schedule() 来调度, 而且每一个tasklet 都是死循环的模式, 那么就很可能出现你的程序永远也不会结束的情况.  (实际上一个tasklet 是 stackless.schedule + 死循环就可能导致程序永不结束)
  * 如果你的所有tasklet 都有可能由于channel.receive 而阻塞, 那么你的程序有可能在还没有任务的是否就退出了. why?  
    “*除了往这个通道发送信息以外，没有其他任何方式可以让这个微进程恢复运行。*“  注意这句。 那么什么情况下会发生这种情况呢?  最常见的是在等待另一个线程给channel.send() 来激活我们的微线程. 或者等待一个异步的操作系统调用返回. (我在使用了stacklesssocket 以后很容易发生这种情况, 具体测试请看test4.py, 改变其中的down_count 可以有不同的行为).

在<a href="http://code.google.com/p/stacklessexamples/wiki/StacklessExamples" target="_blank">StacklessExample</a>s 这个项目里面也有一些复杂一点调度的代码,  实现了sleep 等等功能.