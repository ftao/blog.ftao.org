---
title: 准备写一个有关UPNP的小程序
author: Filia.Tao
layout: post
permalink: /2006/01/02/prepare-to-write-an-upnp-tool/
categories:
  - 端口映射工具的实现
---
我为什么想到要写这个东西？有需求呗。因为最近用电驴，发现linux下 的基于emule的p2p下载软件amule 和xmule 都没有自动端口映射功能。一个偶然的机会，windows下emule非正常退出后，就linux后发现amule竟然获的了高ID。分析原因，由于两边使用的端口相同，emule非正常退出后，没有撤销端口映射，amule就正好用了这个端口。

于是我就蒙发了写一个程序实现UPNP的想法。程序可以添加和删除端口映射，至于端口哪个程序用就由用户决定了。

反正元旦也没什么事，决定在１月４日前争取写出来。