---
title: 进程,线程,协程
author: Filia.Tao
layout: post
permalink: /2009/04/28/process-thread-oroutine/
categories:
  - 编程开发
tags:
  - coroutine
  - process
  - thread
  - 协程
  - 线程
  - 进程
---
最近在学习stackless python , 对进程，线程, 协程等有了一些新的认识.

首先这些东西都是为了”**并发**“, 也就是”**同一时间**“执行多个任务.  不过这三者在实现这一目标上有不同的方法. 

首先三者都构成任务(Task), 都有下面一些特征.

  * 独立的控制流
  * 有内部状态
  * 可以被调度
  * 可以同其他任务通讯 

进程之间不共享任何状态,  进程的调度由操作系统完成, 进程间通讯也要通过操作系统. (共享内存, 管道等等).

进程的问题: 进程间切换开销很大, 通讯麻烦. 

线程之间共享变量, 解决了通讯麻烦的问题, 但是对于变量的访问需要锁.  线程的调度主要也是有操作系统完成, 但用户程序自己有可以有一些控制(比如将自己挂起等). 线程间的切换开销比进程小了很多。

协程的调度完全由用户控制，协程间的切换开销很小, 可以**不加锁**的访问全局变量. 

一个进程可以有多个线程, 一个线程可以有多个协程. 

有这样一个说法,  

> 关于操作系统线程的一般常识是：（1）尽量不要使用它，（2）如果非用不可，就能少用一点就少用一点。而stackless的微线程则使我们从这些限制中解放出来。

这种说法未必完全正确， 但是在实际的情况中确实是使用线程要很小心， 对于共享的变量的访问要小心翼翼的加锁， 同时又要避免死锁.   怎样安全的结束一个线程有时候也是比较麻烦的&#8230;

为什么要加锁? 因为线程的调度是操作系统完成的, 你无法控制它. 它可能在你不希望的时候中断你的程序. 比如

<pre>thread1:
    1: a = [1,2]
    2: print a[1]

    thread2:
    3: a[1] = 3
    4: del a[1]
    5: print a</pre>

上面的代码就不是线程安全的.  执行序列可能是 1,3,4,2,5  或者 其他很多组合.    (例子不太好&#8230;)

当我们使用协程的时候我们自己控制控制流程.

<pre>thread1:
    a = [1,2]
    yield
    print a[1]

    thread2:
    a[1] = 3
    yield
    del a[1]
    print a</pre>

 

参考资料:

1. <a href="http://www.dabeaz.com/coroutines/index.html" target="_blank">A Curious Course on Coroutines and Concurrency</a> ***强烈推荐 ***

2. <a href="http://zh.wikipedia.org/wiki/协程" target="_blank">http://zh.wikipedia.org/wiki/协程</a>

3. <a href="http://l.99081.com/lych/stackless.html" target="_blank">Stackless Python 并发式编程介绍</a>

4. [eurasia][1] 基于国产高性能Python Web 框架   （该项目的<a href="http://groups.google.com/group/eurasia-users" target="_blank">邮件列表</a>有一些高质量的关于stackless python 的讨论)

 

下面一篇预告:  
stackless python  中 tasklet 调度的使用心得

 [1]: http://code.google.com/p/eurasia/
