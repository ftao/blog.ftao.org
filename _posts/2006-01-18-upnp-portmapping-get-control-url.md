---
title: 端口映射之获得控制URL
author: Filia.Tao
layout: post
permalink: /2006/01/18/upnp-portmapping-get-control-url/
categories:
  - 端口映射工具的实现
---
摘要：通过向路由发送 HTTP GET 请求获得设备描述XML，并解析XML获得控制URL.

解析XML文件我使用了 Frank Vanden Berghen 的一个[XML Parser ][1]工具（在GPL下发布）。

我们同样分两步进行，首先下载设备描述文件。（请看[源码][2]中UPNPNAT::get_description()函数）  
1.解析描述文件的URL，获得主机(host)、端口(port)、路径(path).(parseUrl函数)  
2.连接到host:port (tcp_connect 函数)  
3.构造类似

<div align="left">
  <font color="#ff0033"> GET path HTTP/1.1<br /> Host: host:port </font>
</div>

<div align="left">
  的信息（第二行下要一个空行），并通过刚才的TCP 套接字，发送到路由器。（sprintf ,send 函数).
</div>

<div align="left">
  4.接收数据，我使用flag为 MSG_WAITALL的recv函数，函数一直阻塞直到数据全部读完。 数据最终保存在std::string description_info中。
</div>

<div align="left">
  我想通过浏览器下载这个文件的过程是类似的吧。
</div>

<div align="left">
</div>

<div align="left">
  然后，解析这个XML文件。（请看<a href="http://whygudu.iblog.cn/index.php?op=ViewResource&#038;resource=portmapping-0.1.tar.gz">源码</a>中UPNPNAT:: parser_description()函数）
</div>

<div align="left">
  我们找到”root”的”deviceType”是<font color="#ff0066">“urn:schemas-upnp-org:device:InternetGatewayDevice:1&#8243;</font>的”device” childNode ,获得这个”device”的”deviceList”,记为A。
</div>

<div align="left">
  找到A的”deviceType”是<font color="#ff0066">“urn:schemas-upnp-org:device:WANDevice:1&#8243;</font>的”device” childNode ,获得这个”device”的”deviceList”,记为B。
</div>

<div align="left">
  找到B的”deviceType”是<font color="#ff0066">“urn:schemas-upnp-org:device:WANConnectionDevice:1&#8243;</font>的”device” childNode ,获得这个”device”的”serviceList”,记为C。
</div>

<div align="left">
  找到C的”serviceType”是<font color="#ff0066">“urn:schemas-upnp-org:service:WANIPConnection:1&#8243;</font> 或<font color="#ff0066">“urn:schemas-upnp-org:service:WANPPPConnection:1&#8243;</font> 的”service” childNode ,记为D.<br /> 获得D的”controlUrl”保存在std::string control_url中。
</div>

<div align="left">
  但是这里获得control_url一般为相对URL,所以要从”root”下面，找到”URLBase”的值，（如果是空，则用describe_url的”htpp://xxx.xxx.xxx.xxx:xxxx”部分代替.)
</div>

<div align="left">
  最后在相对的control_url前加上URLBasr 获得完整的control_url.
</div>

<div align="left">
</div>

<div align="left">
  至此，第二步“获得控制URL”完成。
</div>

<div align="left">
</div>

 [1]: http://iridia.ulb.ac.be/~fvandenb/tools/xmlParser.html
 [2]: http://whygudu.iblog.cn/index.php?op=ViewResource&#038;resource=portmapping-0.1.tar.gz