---
title: HostMonster 安装 Django
author: Filia.Tao
layout: post
permalink: /2008/02/23/django-on-hostmonste/
categories:
  - python
tags:
  - Django
  - HostMonster
---
这个Blog已经搬到HostMonster .

今天总算在HostMonster主机上成功安装了Django 。

几个步骤记录一下:

  1. 按照这个指南 <a href="http://wiki.dreamhost.com/Unix_account_setup" target="_blank">http://wiki.dreamhost.com/Unix_account_setup</a> 建立了环境。 （这是Dreamhost 的一个Wiki) 不过基本都通用的。
  2. 安装Python 2.5 , 原来的系统上有一个Python 2.3 。 不过我们不能往系统目录装Python 包的。 所有装一个新的.  
    <a href="http://wiki.dreamhost.com/Python" target="_blank"> http://wiki.dreamhost.com/Python</a>  
    基本按照这个。  
    不过我后来总是出现Internal Error , 看error_log 说找不到libpython2.5.so.1 等等。。  
    于是我把下面的这句中的 &#8211;enable-shared这个去掉了</p> 
    <pre>./configure --prefix ${RUN} --enable-shared</pre>
    
    这就好了。 (不过我很怀疑这个有没有关系，因为中间我发现有一个Apache 的graceful 重启, 可能是这个导致我的程序能够工作）
    
    安装Django 参照这两个文档。 （其实基本是一个)  
    <a href="http://jeffcroft.com/blog/2006/may/11/django-dreamhost/" target="_blank"> http://jeffcroft.com/blog/2006/may/11/django-dreamhost/</a>  
    <a href="http://wiki.dreamhost.com/Django" target="_blank"> http://wiki.dreamhost.com/Django</a>  
    只是注意要把dispatch.fcgi 头上的#! 这行改成Python 2.5 的路径</li> 
    
      * It works.  
        我准备用这个做一个Planet Venus 的Web 管理界面。</ol>