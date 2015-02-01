---
title: 关于Django 部署方式的选择
author: Filia.Tao
layout: post
permalink: /2011/05/31/django-deploy-solution/
categories:
  - python
---
最近写了一些Django 项目，尝试了一些部署方式。写一下各种方式的优点和缺点。　

Apache + mod_wsgi  
这个方式是Django 推荐的部署方式，优点是比较简单。但是有两个比较大的缺点  
1. 和virtualenv 的结合比较麻烦  
2. mod_wsgi 会关闭标准输出。如果你的代码里面不小心写了一个 print , 就会出现500错误了。和开发环境的区别比较大。

Apache + mod\_fcgid + mod\_rewrite  
这个方式是对服务器环境要求最低的方式，很多虚拟主机都支持这个方式。  
另外有一个优点 fcgi 进程可以由apache 启动，不需要额外的进程管理工具。

Nginx + fcgi  
这个方式的好处是Nginx 比较轻量级，适合内存比较小的 VPS。  
确定是Django 的FCGI 进程需要自己管理。　　

我在Github 上建立了一个工程，提供了这几种部署方式的模版。

[https://github.com/ftao/django-deploy-template  
][1]

当然我们还有其他的选择，比如nginx + mod_wsgi 之类的。  
我没有尝试，主要是我使用的Linux 发行版里面没有对应的包，不想自己编译。

 [1]: https://github.com/ftao/django-deploy-template