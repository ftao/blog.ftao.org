---
title: 由 dotcloud 引发的思考
author: Filia.Tao
layout: post
permalink: /2011/06/12/some-thoughts-about-dotcloud/
categories:
  - ideas
tags:
  - dotcloud
  - thoughts
---
最近玩了一下dotcloud (http://dotcloud.com) , 非常喜欢。  
之前在公司的时候，有一位IT同学负责发布代码，有一阵非常苦恼。因为系统有很多模块，那几天每天都有很多次发布，每次都需要他SSH到服务器上去敲命令。 （后来这位同学就写了个Web界面的发布系统&#8230;)

发布和部署代码应该是一件简单，愉快的事情，不应该那么痛苦。。  
前几年，开始玩GAE 的时候，GAE 的代码上传，发布方式让我第一次觉得可以发布代码可以这么简单。  
做ezEngage 的时候，自己写fabric 的脚本来发布代码，也能做到一个命令搞定，但是写了很多额外的代码来到达这个目的。 另外mysql/nginx 等等还是手工配置的。  
这次尝试用dotcloud, 非常简单，我将之前的[火车票API][1]的那个小项目拿过来尝试部署到dotcloud 上面，按照教程一步成功。 更好的是包括mysql 等等的常用的组件也可以一个命令部署。 

我很喜欢dotcloud 的一个原因是这是个**开发者给开发者创造的产品**。  
界面很简单，主要的功能通过一个(命令行)客户端 来操作API来实现。网站很简单，没有复杂的管理后台。 

而我做的ezEngage有一个很大的问题，产品的主要的客户和我完全不是同类的人。  
如果有下一次，我一定做一些给自己用，给开发者用的东西。

 [1]: https://github.com/ftao/cn-train-tool
