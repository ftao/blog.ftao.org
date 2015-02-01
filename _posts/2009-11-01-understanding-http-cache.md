---
title: '理解HTTP &#8211; 缓存'
author: Filia.Tao
layout: post
permalink: /2009/11/01/understanding-http-cache/
categories:
  - http
  - 编程开发
tags:
  - cache
  - http
---
好吧。 先讲一个我相对熟悉一点的主题吧—缓存。  
服务器端返回的响应中的和缓存相关的头有下面这些:

  * Cache-Control 缓存控制
  * Expires 过期时间
  * Last-Modified 最后修改时间
  * ETag 可以看成是资源的哈希值，资源改变,ETag 也改变。(一个可能的实现是将文件系统的最后修改时间做个哈希)

客户端发送的请求中和缓存有关的头有下面这些:

  * Cache-Control 缓存控制
  * If-None-Match 同ETag 匹配，将上次收到的ETag 发送会服务器，如果匹配返回304
  * If-Modified-Since 同Last-Modified 匹配，将上次受到的最后修改时间发送会服务器，如果没有变化，返回304

下面通过几个场景来说明一下：

#### 第一个场景：图片

大部分情况下网站的图片都是不会变的。一般都会设置一个很长的过期时间。  
比如说Google 首页的logo

> <pre>filia@debian-kde:~$ curl -I http://www.google.cn/intl/zh-CN/images/logo_cn.gif
HTTP/1.1 200 OK
Content-Type: image/gif
Last-Modified: Tue, 05 May 2009 16:40:06 GMT
Date: Thu, 29 Oct 2009 09:39:54 GMT
Expires: Fri, 29 Oct 2010 09:39:54 GMT
X-Content-Type-Options: nosniff
Server: gws
Content-Length: 7763
Cache-Control: public, max-age=31536000
Age: 16383
X-XSS-Protection: 0</pre>

注意Expires（时间正好是一年后）和 Cache-Control （注意max-age的值正好是1年的秒数），这张图片的过期时间是一年以后。 同时Cache-Control 头有一个关键字 public , 含义是图片可以被缓存代理服务器缓存。（比如几年前还在学校用代理上网的时候，学校的代理服务器会将这样的响应缓存下来。)  
如果你刷新Google 首页，不会有针对这张图片的请求发出 — 节省了网络开销。  
我们来按Ctrl+F5 看看发生了什么。  
浏览器发送了下面的请求。注意发出的请求中的Cache-Control:max-age=0 就是这个东西用来告诉浏览器和中间可能存在的缓存代理服务器跳过缓存。

> <pre>GET /intl/zh-CN/images/logo_cn.gif HTTP/1.1
Host: www.google.cn
Connection: keep-alive
Referer: http://www.google.cn/webhp?rls=ig
Cache-Control: max-age=0
If-Modified-Since: Tue, 05 May 2009 16:40:06 GMT</pre>

Google 返回

> <pre>HTTP/1.1 304 Not Modified
Last-Modified: Tue, 05 May 2009 16:40:06 GMT
X-Content-Type-Options: nosniff
Date: Thu, 29 Oct 2009 15:11:05 GMT
Server: gws
X-XSS-Protection: 0</pre>

#### 第二个场景：JS/CSS 文件

网站的Javascript 和 CSS 文件可能会变化，但是不经常变。  
比如说feed.filia.cn 引用的一个js 文件。http://feed.feedsky.com/js/xsl.js 。（在浏览器打开http://feed.filia.cn，使用fiddler 查看网络请求。）  
获得响应如下

> <pre>HTTP/1.1 200 OK
Date: Thu, 29 Oct 2009 14:43:03 GMT
Server: Apache/2.2.3 (Unix) PHP/5.2.0
Last-Modified: Tue, 24 Apr 2007 14:10:29 GMT
ETag: "12ec06c-767-5913df40"
Accept-Ranges: bytes
Content-Length: 1895
Content-Type: application/x-javascript</pre>

我刷新一下页面,发送了下面的请求。(简单起见，删除了一些无关的头)

> <pre>GET /js/xsl.js HTTP/1.1
Host: feed.feedsky.com
Connection: keep-alive
Cache-Control: max-age=0
Accept: */*
If-None-Match: "12ec06c-767-5913df40"
If-Modified-Since: Tue, 24 Apr 2007 14:10:29 GMT</pre>

得到的响应是 (只有header ,没有 body &#8211; 节省了网络开销)。

> <pre>HTTP/1.1 304 Not Modified
Date: Thu, 29 Oct 2009 14:45:34 GMT
Server: Apache/2.2.3 (Unix) PHP/5.2.0
Connection: Keep-Alive
Keep-Alive: timeout=5, max=100
ETag: "12ec06c-767-5913df40"</pre>

总结一下:  
服务器通过Cache-Control , Expires 头来通知客户端是否可以缓存， 缓存到什么时候。  
服务器端通过Last-Modified, ETag 来表示资源的版本， 客户端可以通过If-Modified-Since,If-None-Match 头来通知服务器上次获取资源时的版本， 服务器如果发现资源没有变化， 返回304。  
客户端通过Cache-Control: max-age=0 , 或者 Cache-Control: no-cache 或者其它类似的头来跳过缓存。前者是要求重新验证(服务器可能返回304)，后者要求资源的内容也必须返回回来。 

参考:  
RFC2616 <a rev="Section" href="http://www.w3.org/Protocols/rfc2616/rfc2616.html" target="_blank">Hypertext Transfer Protocol &#8212; HTTP/1.1</a> <a href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13" target="_blank"> 13.Caching  in HTTP</a>