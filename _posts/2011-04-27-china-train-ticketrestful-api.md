---
title: 火车余票查询的RESTful API
author: Filia.Tao
layout: post
permalink: /2011/04/27/china-train-ticketrestful-api/
categories:
  - python
  - Web开发
  - 编程开发
tags:
  - api
  - python
  - RESTful
  - 余票
---
前几天出去玩， 做了几次火车。用来几次 12306.cn 。 余票查询还是挺有用的。 作为API控，花一点时间写了个查询余票的Web API 。  
用法: `http://trans.labs.ftao.org/api/yupiao.json?date={date}&#038;start={start_station}&#038;arrive={arrive_station}`  
比如4.29日上海到南京的余票

`curl "http://train.labs.ftao.org/api/yupiao.html?date=2011-04-29&#038;start=%E4%B8%8A%E6%B5%B7&#038;arrive=%E5%8D%97%E4%BA%AC"<br />
      `

存在的问题:很慢，数据不稳定（同一个查询会有短时间内会有不同的结果）。不过这都是 12306.cn 的问题。  
简单的界面: <http://train.labs.ftao.org/ui/>  
有空用JQuery Mobile 写一个适合移动设备的界面。 （这个才是我真正的需求）

代码: <https://github.com/ftao/cn-train-tool>
