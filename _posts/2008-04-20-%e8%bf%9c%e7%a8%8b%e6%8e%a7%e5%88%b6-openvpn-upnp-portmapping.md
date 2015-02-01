---
title: 远程控制 + openvpn + upnp portmapping
author: Filia.Tao
layout: post
permalink: /2008/04/20/%e8%bf%9c%e7%a8%8b%e6%8e%a7%e5%88%b6-openvpn-upnp-portmapping/
categories:
  - 随便写写
tags:
  - loginany
  - openvpn
  - remote
  - upnp
---
前天需要远程访问一台机器(或其所在局域网), 从 QQ 远程 -><a href="http://blog.ftao.org/wp-admin/www.loginany.com" target="_blank"> LoginAny</a>-> 端口映射 + openvpn 总算搞定.

期间特别是端口映射部分，自己以前写的那个工具实在是太差劲,  找了好多个最后找个勉强能用的- <a href="http://sourceforge.net/projects/upnpportforward/" target="_blank">PortForward</a>

第二天偶然发现一个完全符合我的期望的命令行工具，更我以前写的那个目地完全一样.  强烈推荐 <a href="http://miniupnp.free.fr/" target="_blank">MiniUPnP Project </a>

对于openvpn 的配置 可以看<a href="http://www.huaidan.org/blog/article.asp?id=1617" target="_blank">这篇文章</a> ,足够了.
