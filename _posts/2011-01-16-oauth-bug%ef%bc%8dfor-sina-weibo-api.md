---
title: 新浪微博OAuth 认证的bug
author: Filia.Tao
layout: post
permalink: /2011/01/16/oauth-bug%ef%bc%8dfor-sina-weibo-api/
categories:
  - 编程开发
tags:
  - 443
  - basestring
  - oauth
  - sina
  - weibo
  - 新浪微博
---
新浪微博API 的 OAuth 认证在对于https 的请求在计算basestring 的时候似乎有问题。  
根据<http://tools.ietf.org/html/rfc5849#section-3.4.1.2> . 

> 3. The port MUST be included if it is not the default port for the  
> scheme, and MUST be excluded if it is the default. Specifically,  
> the port MUST be excluded when making an HTTP request [RFC2616]  
> to port 80 or when making an HTTPS request [RFC2818] to port 443.  
> All other non-default port numbers MUST be included. 

但是新浪似乎在计算的时候还是把443 这个端口放进去了。  
比如下面的请求, 我发的请求被拒绝了， 然后新浪在返回的头里面给出它计算出来的basestring, 其中端口443 被包含在里面。 

> `<br />
send: 'POST /oauth/request_token HTTP/1.1\r\nHost: api.t.sina.com.cn:443\r\nAccept-Encoding: identity\r\nauthorization: OAuth realm="", oauth_nonce="58426982", oauth_timestamp="1295205930", oauth_consumer_key="hide-my-consumer-key", oauth_signature_method="HMAC-SHA1", oauth_version="1.0", oauth_signature="waNR3csXxBeQk6krLYEgbOnCpaA%3D", oauth_callback="http%3A%2F%2Ftest-client%2Fcallback"\r\nuser-agent: Python-httplib2/$Rev$\r\n\r\n'<br />
reply: 'HTTP/1.1 401 Unauthorized\r\n'<br />
header: Pragma: No-cache<br />
header: Cache-Control: no-cache<br />
header: Expires: Thu, 01 Jan 1970 00:00:00 GMT<br />
header: WWW-Authenticate: OAuth realm="http%3A%2F%2Fxd110", oauth_signature="waNR3csXxBeQk6krLYEgbOnCpaA%3D",<br />
oauth_signature_base_string="<br />
POST%26http%253A%252F%252F<strong>api.t.sina.com.cn%253A443</strong>%252Foauth%252Frequest_token%26<br />
oauth_callback%253Dhttp%25253A%25252F%25252Ftest-client%25252Fcallback%2526oauth_consumer_key%253Dhide-my-consumer-key%2526oauth_nonce%253D58426982%2526oauth_signature_method%253DHMAC-SHA1%2526oauth_timestamp%253D1295205930%2526oauth_version%253D1.0", oauth_signature_method="HMAC-SHA1", oauth_right_signature="83aYWqB0atNi3vhwnugb0fGnH3k%3D"<br />
header: Content-Length: 185<br />
header: Server: weibo<br />
header: Date: Sun, 16 Jan 2011 11:25:36 GMT<br />
header: X-Varnish: 729485806<br />
header: Age: 0<br />
header: Via: 1.1 varnish<br />
header: Connection: close<br />
` 

在这个问题上面浪费我不少时间。