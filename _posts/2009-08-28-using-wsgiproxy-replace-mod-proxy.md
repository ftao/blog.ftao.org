---
title: 使用 wsgiproxy 转发请求
author: Filia.Tao
layout: post
permalink: /2009/08/28/using-wsgiproxy-replace-mod-proxy/
categories:
  - python
  - 随便写写
tags:
  - proxy
  - python
  - wsgi
---
<a title="WSGI Proxy" href="http://pythonpaste.org/wsgiproxy/" target="_blank">WSGIProxy</a> 是一个 python 包,  属于 paster  的一个组件， 提供了代理功能。可以在一些情况下代替mod_proxy.

比如你在本地6543端口运行一个Python Web 服务器, 而你的服务器只开放了80端口，并且运行着apache , 你可以使用mod\_proxy 来把请求转发过去,  或者你也可以使用 mod\_rewrite + fcgi + 下面的脚本实现同样的功能.

proxy.fcgi

<div class="wp_syntax">
  <div class="code">
    <pre class="python" style="font-family:monospace;"><span style="color: #808080; font-style: italic;">#!/home/filia/env/bin/python</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">sys</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">os</span>
&nbsp;
script_name = <span style="color: #483d8b;">'/'</span> + <span style="color: #dc143c;">os</span>.<span style="color: black;">path</span>.<span style="color: black;">basename</span><span style="color: black;">&#40;</span>__file__<span style="color: black;">&#41;</span>
<span style="color: #dc143c;">os</span>.<span style="color: black;">environ</span><span style="color: black;">&#91;</span><span style="color: #483d8b;">'PYTHON_EGG_CACHE'</span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">"/home/filia/tmp/.egg_cache/"</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">import</span> wsgiproxy.<span style="color: black;">exactproxy</span>
<span style="color: #ff7700;font-weight:bold;">from</span> fcgi <span style="color: #ff7700;font-weight:bold;">import</span> WSGIServer
&nbsp;
DEST_SERVER = <span style="color: #483d8b;">"127.0.0.1"</span>
DEST_PORT = <span style="color: #ff4500;">6543</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> reverse_proxy<span style="color: black;">&#40;</span>environ, start_response<span style="color: black;">&#41;</span>:
    environ<span style="color: black;">&#91;</span><span style="color: #483d8b;">'SERVER_NAME'</span><span style="color: black;">&#93;</span> = DEST_SERVER
    environ<span style="color: black;">&#91;</span><span style="color: #483d8b;">'SERVER_PORT'</span><span style="color: black;">&#93;</span> = DEST_PORT
    <span style="color: #ff7700;font-weight:bold;">if</span> environ.<span style="color: black;">has_key</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'SCRIPT_NAME'</span><span style="color: black;">&#41;</span> <span style="color: #ff7700;font-weight:bold;">and</span> environ<span style="color: black;">&#91;</span><span style="color: #483d8b;">'SCRIPT_NAME'</span><span style="color: black;">&#93;</span>.<span style="color: black;">endswith</span><span style="color: black;">&#40;</span>script_name<span style="color: black;">&#41;</span>:
        environ<span style="color: black;">&#91;</span><span style="color: #483d8b;">'SCRIPT_NAME'</span><span style="color: black;">&#93;</span> = environ<span style="color: black;">&#91;</span><span style="color: #483d8b;">'SCRIPT_NAME'</span><span style="color: black;">&#93;</span><span style="color: black;">&#91;</span>:-<span style="color: #008000;">len</span><span style="color: black;">&#40;</span>script_name<span style="color: black;">&#41;</span><span style="color: black;">&#93;</span>
    <span style="color: #ff7700;font-weight:bold;">return</span> wsgiproxy.<span style="color: black;">exactproxy</span>.<span style="color: black;">proxy_exact_request</span><span style="color: black;">&#40;</span>environ, start_response<span style="color: black;">&#41;</span>
&nbsp;
WSGIServer<span style="color: black;">&#40;</span>reverse_proxy<span style="color: black;">&#41;</span>.<span style="color: black;">run</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span></pre>
  </div>
</div>

当然你还需要一个 fcgi.py 模块， 比如这个 <a href="http://svn.saddi.com/py-lib/trunk/fcgi.py" target="_blank">http://svn.saddi.com/py-lib/trunk/fcgi.py</a> .  
还有一个.htacces 文件定义rewrite.

<div class="wp_syntax">
  <div class="code">
    <pre class="conf" style="font-family:monospace;">AddHandler fcgid-script .fcgi
RewriteEngine On
RewriteBase /
RewriteRule ^(proxy\.fcgi/.*)$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*)$ proxy.fcgi/$1 [L]</pre>
  </div>
</div>

我没有测试这样的方式的性能, 估计不会好到哪里去。 不过我的应用是一个管理后台， 性能就不是一个重要的问题了.
