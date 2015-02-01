---
title: UPNP 的原理
author: Filia.Tao
layout: post
permalink: /2006/01/14/upnp-portmapping-principle/
categories:
  - 端口映射工具的实现
---
UPNP的全称是<font size="-1"> Universal plug-and-play( 通用即插即用).UPnP 是针对智能家电、无线设备以及各种外观尺寸的个人电脑的普遍对等（peer-to-peer）网络连接而设计的一种架构。它旨在为家庭、小型企业、公共场所中或连接到互联网的ad-hoc 网或未管理网络提供易于使用、灵活且基于标准的连接。</font>(引自[这里][1].)

我们这里用到的自动端口映射只是UPNP的一个小应用。按照UPNP的相关规范，UPNP网络的第０步是寻址（获得一个IP地址，在我要解决的问题中这不是一个问题。）  
第１步是发现，控制点在网上搜索感兴趣的设备，而设备向网络中的控制点宣告其服务。对于自动端口映射来说就是发现带UPNP功能的路由器。

第２步是描述。在第１步中我们往往能获得一个设备的描述URL,在第２步中我们要通过一个URL，下载一个XML文件。并从中找到有关设备的类型，服务类型，控制URL,事件触发URL等。

第３步是控制。通过第２步获得的控制URL，通过向其发送控制消息（同样用XML描述）来实现某些功能。对于自动端口映射来说就是查看、增加、删除等。

第４步事件触发和第５步展示在自动端口映射没用用到。有兴趣可以自己看文档。

 [1]: http://www.upnp.org/resources/documents/UDA1.0-Chinese_.pdf