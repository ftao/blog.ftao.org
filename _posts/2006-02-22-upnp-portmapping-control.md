---
title: 端口映射之发送控制信息
author: Filia.Tao
layout: post
permalink: /2006/02/22/upnp-portmapping-control/
categories:
  - 端口映射工具的实现
---
摘要：在获得控制URL后，发送添加、删除、察看端口映射的控制信息。这里主要讨论控制信息的格式。

在这里我先把各种控制信息的格式说明一下。（下面的rn都是表示换行，我输入不了反斜杠。）

>   * 增加端口映射。 “AddPortMapping”
>   * “<NewRemoteHost></NewRemoteHost>rn” “<NewExternalPort>***ExternalPort***</NewExternalPort>rn” “<NewProtocol>***Protocol***</NewProtocol>rn” “<NewInternalPort>***InternalPort***</NewInternalPort>n”  
>     “<NewInternalClient>***InternalClient***</NewInternalClient>rn”  
>     “<NewEnabled>1</NewEnabled>rn”  
>     “<NewPortMappingDescription>***PortMappingDescription***” “</NewPortMappingDescription>rn”  
>     “<NewLeaseDuration>***LeaseDuration***</NewLeaseDuration>rn”
>   * 删除端口映射　”DeletePortMapping”
>   * “<NewRemoteHost></NewRemoteHost>rn” “<NewExternalPort>***ExternalPort***</NewExternalPort>rn” “<NewProtocol>***Protocol***</NewProtocol>rn”
>   * 获得端口映射信息　”GetGenericPortMappingEntry”
>   * “<NewPortMappingIndex>***PortMappingIndex***</NewPortMappingIndex>” “<NewRemoteHost></NewRemoteHost>rn” “<NewExternalPort></NewExternalPort>rn” “<NewProtocol></NewProtocol>rn” “<NewInternalPort></NewInternalPort>rn” “<NewInternalClient></NewInternalClient>rn” “<NewEnabled>1</NewEnabled>rn” “<NewPortMappingDescription>” “</NewPortMappingDescription>rn” 　”<NewLeaseDuration></NewLeaseDuration>rn”

其中斜体部分需要在编程是填入的。ExternalPort 外部端口。InternalPort内部端口。这 两者一般就填映射的端口。Protocal 填TCP或UDP。InterClient 一般就是本地IP地址。PortMappingDescription 填写端口映射的描述，比如什么程序建立了这个端口。LeaseDuration 是映射的持续时间，用0表示不永久。PortMappingIndex 是端口映射索引，路由上第几个映射。

我们再来看下面这个XML文档结构。

“<?xml version=”1.0&#8243; encoding=”utf-8&#8243;?>rn”  
“<s:Envelope xmlns:s=”  
“”http://schemas.xmlsoap.org/soap/envelope/” ”  
“s:encodingStyle=”  
“”http://schemas.xmlsoap.org/soap/encoding/”>rn”  
“<s:Body>rn”  
“<u:***actionName*** xmlns:u=”***serviceType***“>rn”  
“***actionParams***</u:***actionName***>rn”  
“</s:Body>rn”  
“</s:Envelope>rn”

我们在actionName 处填入”AddPortMapping” “DeletePortMapping” “GetGenericPortMappingEntry”。serviceType 处填入设备的[服务类型][1]。”urn:schemas-upnp-org:service:WANIPConnection:1&#8243;或”urn:schemas-upnp-org:service:WANPPPConnection:1&#8243;。actionParams 填入上面的各种控制信息。

最后在前面加上HTTP头。

“POST ***path*** HTTP/1.1rn”  
“HOST: ***host***:***port***rn”  
“SOAPACTION:”***serviceType***#***actionName***“rn”  
“CONTENT-TYPE: text/xml ; charset=”utf-8&#8243;rn”  
“Content-Length: ***contentLength*** rnrn”

path host port 意思很明显。contentLength面那个XML文档的长度。

然后连接到host:port，发送到整个信息即可完成控制。

 [1]: http://whygudu.iblog.cn/index.php?op=ViewArticle&#038;articleId=40835