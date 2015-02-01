---
title: 使用Django的一些小经验
author: Filia.Tao
layout: post
permalink: /2011/04/17/django-tips/
categories:
  - python
  - 编程开发
tags:
  - Django
  - piston
  - sentry
  - signal
---
分享一些使用Django 的小经验:

  * <a href="https://github.com/dcramer/django-sentry" target="_blank">django-sentry</a> 用来记录异常信息很棒，另外配合logging 模块做错误日志也很好用。（关键时它把没一层函数调用的局部变量都记录下来了，找Bug 很方便) 
  * Signal 可以将代码解耦。 比如说支付流程，有一个支付宝接口的app, 受到支付宝的通知，验证数据，然后发出一个Signal , 别的app connect 这个Signal , 然后更新订单状态之类的。 这个支付宝app 就完全是一个 reusable app . 
  * Django 的model 来做报表还是很不方便的，这种情况下用 Raw SQL 比较好。
  * [Piston][1] 用来写RESTful API 很方便。
  * [South][2] is great . 

&#8211;EOF&#8211;

 [1]: https://bitbucket.org/jespern/django-piston/wiki/Home
 [2]: http://south.aeracode.org/