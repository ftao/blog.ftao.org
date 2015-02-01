---
title: Android 多次打开创建WebView 后崩溃的问题
author: Filia.Tao
layout: post
permalink: /2010/06/14/android-many-webview-cause-cras/
categories:
  - 随便写写
tags:
  - android
  - WebView
---
最近工作中在做Android 相关的开发， 碰到一个问题。 程序在我们一个同事的G2手机上不停的崩溃。最终发现是1.5/1.6 的Android 的WebView 多次打开，关闭，再打开之后，接着使用轨迹球来操作，libwebcore.so 就很容易崩溃。  
Google 了一把 发现和这个 <http://code.google.com/p/fbconnect-android/issues/detail?id=23&#038;can=1&#038;q=destroy>Issue 很类似。 手工在Activity 销毁的时候调用一下webView.destory() . 问题解决。 

在这里做一个记录， 如果有后来人碰到同样问题，可以试一下这个办法。  
关键词：Android, WebView, libwebcore.so 崩溃 。