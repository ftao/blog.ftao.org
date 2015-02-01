---
title: DNS 原来保存在 /etc/resolv.conf
author: Filia.Tao
layout: post
permalink: /2006/01/19/where-dns-info-is-saved/
categories:
  - Linux
  - 随便写写
---
自从上次拨号上了一次网以后，恢复到通过局域网上网时。发现每次启动的时候，DNS的设置总没有了。每次都要重新设，郁闷了好久。但又懒得去找原因.

今天总算有空，看了/etc/init.d/network，/etc/sysconfig/network-script/ 下的几个脚本。  
不好意思，没有找到问题。

最后发现，原来DNS 就保存在/etc/resolv.conf 中，上次拨过号以后，/etc/resolv.conf 变成了指向/etc/ppp/resolv.conf 的符号链接。而/etc/ppp/resolv.conf 是/var/run/ppp/resolv.conf 的符号链接.难怪一重启就没了。手动将/etc/init.d/resolv.cong改成  
普通文件。在设置好，重启没问题了。

有些事情，没有我想象的那么复杂。