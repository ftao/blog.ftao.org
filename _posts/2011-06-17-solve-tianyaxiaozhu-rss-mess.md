---
title: 解决天涯小筑的RSSFeed 乱码问题
author: Filia.Tao
layout: post
permalink: /2011/06/17/solve-tianyaxiaozhu-rss-mess/
categories:
  - python
tags:
  - redis
  - rss
  - web.py
  - 乱码
  - 天涯小筑
---
不知道从哪一天开始 <a href="http://donatino.skygate.cn/" target="_blank">天涯小筑</a>的标题就是是乱码的了。 （在GoogleReader里面或者直接打开都是标题乱码。)

昨天决定不再忍受，于是就写了一个程序把RSS Feed 抓过来，然后把乱码清理好。  
清理过的RSS Feed 放在 http://fun.ftao.dotcloud.com/cache/rss/tyxz.xml 。 （由于天涯小筑本身的网站似乎不是很稳定，我把Feed的更新时间设置为每半小时更新一次。) 

Code: <https://github.com/ftao/fun/>  
这种微型项目用web.py 很合适， 顺便用了一下 redis 作为缓存。