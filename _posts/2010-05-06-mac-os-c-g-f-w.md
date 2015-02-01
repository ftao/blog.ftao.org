---
title: MAC OS 翻＊墙
author: Filia.Tao
layout: post
permalink: /2010/05/06/mac-os-c-g-f-w/
categories:
  - 随便写写
tags:
  - FQVPN
  - MAC
  - proxy
  - SSH
  - VPN
---
刚刚入手了一个 MBP， 第一件事情就是怎么研究怎么 翻＊墙。 最终还是两个方式比较靠谱。 

1. 用SSH 做 SOCK5 代理。 这个和Linux/Windows 下都是差不多的。 这个晚上教程很多。 我就不多废话了。  
参考

http://www.chedong.com/blog/archives/001246.html

http://d2zhongguo.com/bbs/viewthread.php?tid=942

简单来说就是一个命令  
ssh -Nf -D 7070 username@remote_host  
输入密码， 就在 7070 端口建立一个Socks 代理。 Mac 下 Firefox (可能)需要 在 about:config 里面修改 network.proxy.socks\_remote\_dns 为true.

2. OpenVPN  
MAC 下的OpenVPN 的客户端可以用 Tunnelblick。  
可以在 http://code.google.com/p/tunnelblick/ 下载到。  
参考这篇文章。 

http://www.helsinki.fi/atk/english/hy-ppp/hy-vpn/hy-vpn-mac.html

安装以后，我们需要一个OpenVPN 的服务器帐号。 我选择到 [http://www.fqvpn.net ][1]注册了一个帐号，获得一个试用帐号。 然后从 <http://www.fqvpn.net/vpn/openvpn> 这里下载配置文件。将其放到配置文件夹。默认应该是  
~/Library/Application Support/Tunnelblick/Configurations  
之后运行Tunnelblick , 选择连接到 ”client” (这个是VPN 的名字， 也就是配置文件的名字). FQVPN 的帐号是用户名／密码认证的， 所以需要输入用户名和密码。 (用户名和密码可以在 www.fqvpn.net 的”我的帐号“中看到。）等一下就可以连上VPN 了。  
如果你有需要配置国内路由表， 请参考 http://code.google.com/p/chnroutes/

 [1]: http://www.fqvpn.net
