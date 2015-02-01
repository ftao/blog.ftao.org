---
title: 端口映射之发现支持UPNP的路由
author: Filia.Tao
layout: post
permalink: /2006/01/15/upnp-portmapping-discover-device/
categories:
  - 端口映射工具的实现
---
摘要：使用标准的多播地址 239.255.255.250:1900,利用简单服务发现协议 SSDP 搜索UPNP设备。编程中使用数据报套接字。（这里下载[源码][1]）

发现这个过程主要有两步。第一，使用数据报套接字向239.255.255.250:1900，发送一条多播请求，格式如下

> > <font color="#ff00ff">M-SEARCH * HTTP/1.1</font>  
> > <font color="#ff00ff">HOST: 239.255.255.250:1900</font>  
> > <font color="#ff00ff">MAN:”ssdp:discover”</font>  
> > <font color="#ff00ff">MX:3</font>  
> > <font color="#ff00ff">ST:UPnP:rootdevice</font>

这个多播请求的含义如下：M-SEARCH SSDP协议定义的搜索请求方法。HOST必须是这个多播地址。MAN的值也必须是”ssdp:discover” 不可少了双引号。MX的含义是最长等待时间，可以自己设置。ST表示search target 搜索目标。我们在这里用找根设备。另外在编程中我们要在每一行后面加上”rn” 表示换行。（详见源码 UPNPNAT.discovery()).

第二步，如果你的网络存在一个UPNP设备的话，为了被找到，设备必须向发送查找请求的多播通道的源 IP 地址与端口发送响应信息。所以你可以从239.255.255.250:1900这个地址接收到响应消息。类似下面的消息。

> > <font color="#ff3399">HTTP/1.1 200 OK</font>  
> > <font color="#ff3399">CACHE-CONTROL: max-age=100</font>  
> > <font color="#ff3399">DATE: Sun, 15 Jan 2006 06:51:02 GMT</font>  
> > <font color="#ff3399">EXT:</font>  
> > <font color="#ff3399">LOCATION: http://192.168.14.1:1900/igd.xml</font>  
> > <font color="#ff3399">SERVER: TP-LINK Wireless Router WR541G/5, UPnP/1.0</font>  
> > <font color="#ff3399">ST: upnp:rootdevice</font>  
> > <font color="#ff3399">USN:uuid:upnp-InternetGatewayDevice-192168141678900001::upnp:rootdevice</font>

接下来我们要从里面获得我们要的消息。首先，我们必须找到” 200 OK “,说明没有错误发生，否则一切免谈。接着，我们要找到LOCATION项，获得设备描述URL。（程序中的处理归根到底就是一个子字符的查找。）

到这里，我们的第一步“发现”完成。

 [1]: http://whygudu.iblog.cn/index.php?op=ViewResource&#038;resource=portmapping-0.1.tar.gz
