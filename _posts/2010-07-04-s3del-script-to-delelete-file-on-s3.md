---
title: 删除S3上文件的脚本s3del
author: Filia.Tao
layout: post
permalink: /2010/07/04/s3del-script-to-delelete-file-on-s3/
categories:
  - python
tags:
  - amazon
  - boto
  - delete
  - python
  - s3
---
之间写过一个讲文件备份到Amazon S3 的[文章][1]。 之后就有新的需求比如定期清理很久之前的备份（比如一个月前）。  
Boto 库里面有一个s3put 的脚本，但是没有删除文件的脚本。 于是自己写了个s3del, 代码如下 。

<div class="wp_syntax">
  <div class="code">
    <pre class="python" style="font-family:monospace;"><span style="color: #808080; font-style: italic;">#!/home/ftao/env/bin/python</span>
<span style="color: #808080; font-style: italic;"># Copyright (c) 2010 Tao Fei http://ftao.org/</span>
<span style="color: #808080; font-style: italic;"># Based on boto's s3put scirpt </span>
&nbsp;
<span style="color: #808080; font-style: italic;"># Copyright (c) 2006,2007,2008 Mitch Garnaat http://garnaat.org/</span>
<span style="color: #808080; font-style: italic;">#</span>
<span style="color: #808080; font-style: italic;"># Permission is hereby granted, free of charge, to any person obtaining a</span>
<span style="color: #808080; font-style: italic;"># copy of this software and associated documentation files (the</span>
<span style="color: #808080; font-style: italic;"># "Software"), to deal in the Software without restriction, including</span>
<span style="color: #808080; font-style: italic;"># without limitation the rights to use, copy, modify, merge, publish, dis-</span>
<span style="color: #808080; font-style: italic;"># tribute, sublicense, and/or sell copies of the Software, and to permit</span>
<span style="color: #808080; font-style: italic;"># persons to whom the Software is furnished to do so, subject to the fol-</span>
<span style="color: #808080; font-style: italic;"># lowing conditions:</span>
<span style="color: #808080; font-style: italic;">#</span>
<span style="color: #808080; font-style: italic;"># The above copyright notice and this permission notice shall be included</span>
<span style="color: #808080; font-style: italic;"># in all copies or substantial portions of the Software.</span>
<span style="color: #808080; font-style: italic;">#</span>
<span style="color: #808080; font-style: italic;"># THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS</span>
<span style="color: #808080; font-style: italic;"># OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABIL-</span>
<span style="color: #808080; font-style: italic;"># ITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT</span>
<span style="color: #808080; font-style: italic;"># SHALL THE AUTHOR BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, </span>
<span style="color: #808080; font-style: italic;"># WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,</span>
<span style="color: #808080; font-style: italic;"># OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS</span>
<span style="color: #808080; font-style: italic;"># IN THE SOFTWARE.</span>
<span style="color: #808080; font-style: italic;">#</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">getopt</span>, <span style="color: #dc143c;">sys</span>, <span style="color: #dc143c;">os</span>
<span style="color: #ff7700;font-weight:bold;">import</span> boto
<span style="color: #ff7700;font-weight:bold;">from</span> boto.<span style="color: black;">exception</span> <span style="color: #ff7700;font-weight:bold;">import</span> S3ResponseError
&nbsp;
usage_string = <span style="color: #483d8b;">"""
SYNOPSIS
    s3del [-a/--access_key &lt;access_key&gt;] [-s/--secret_key &lt;secret_key&gt;]
          -b/--bucket &lt;bucket_name&gt; 
          [-d/--debug &lt;debug_level&gt;]
          [-n/--no_op] [-q/--quiet]
          path
&nbsp;
    Where
        access_key - Your AWS Access Key ID.  If not supplied, boto will
                     use the value of the environment variable
                     AWS_ACCESS_KEY_ID
        secret_key - Your AWS Secret Access Key.  If not supplied, boto
                     will use the value of the environment variable
                     AWS_SECRET_ACCESS_KEY
        bucket_name - The name of the S3 bucket the file(s) should be
                      delete from.
        path - A path to a file or folder (must ends with '/') on s3 
        debug_level - 0 means no debug output (default), 1 means normal
                      debug output from boto, and 2 means boto debug output
                      plus request/response output from httplib
&nbsp;
     If the -n option is provided, no files will be deleted from S3 but
     informational messages will be printed about what would happen.
"""</span>
<span style="color: #ff7700;font-weight:bold;">def</span> usage<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>:
    <span style="color: #ff7700;font-weight:bold;">print</span> usage_string
    <span style="color: #dc143c;">sys</span>.<span style="color: black;">exit</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> main<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>:
    <span style="color: #ff7700;font-weight:bold;">try</span>:
        opts, args = <span style="color: #dc143c;">getopt</span>.<span style="color: #dc143c;">getopt</span><span style="color: black;">&#40;</span><span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span>:<span style="color: black;">&#93;</span>, <span style="color: #483d8b;">'a:b::d:hn:qs:v'</span>,
                                   <span style="color: black;">&#91;</span><span style="color: #483d8b;">'access_key'</span>, <span style="color: #483d8b;">'bucket'</span>, <span style="color: #483d8b;">'debug'</span>, <span style="color: #483d8b;">'help'</span>, 
                                    <span style="color: #483d8b;">'no_op'</span>, <span style="color: #483d8b;">'quiet'</span>, <span style="color: #483d8b;">'secret_key'</span> <span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">except</span>:
        usage<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
    aws_access_key_id = <span style="color: #008000;">None</span>
    aws_secret_access_key = <span style="color: #008000;">None</span>
    bucket_name = <span style="color: #483d8b;">''</span>
    debug = <span style="color: #ff4500;"></span>
    quiet = <span style="color: #008000;">False</span>
    no_op = <span style="color: #008000;">False</span>
    <span style="color: #ff7700;font-weight:bold;">for</span> o, a <span style="color: #ff7700;font-weight:bold;">in</span> opts:
        <span style="color: #ff7700;font-weight:bold;">if</span> o <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: black;">&#40;</span><span style="color: #483d8b;">'-h'</span>, <span style="color: #483d8b;">'--help'</span><span style="color: black;">&#41;</span>:
            usage<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
            <span style="color: #dc143c;">sys</span>.<span style="color: black;">exit</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
        <span style="color: #ff7700;font-weight:bold;">if</span> o <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: black;">&#40;</span><span style="color: #483d8b;">'-a'</span>, <span style="color: #483d8b;">'--access_key'</span><span style="color: black;">&#41;</span>:
            aws_access_key_id = a
        <span style="color: #ff7700;font-weight:bold;">if</span> o <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: black;">&#40;</span><span style="color: #483d8b;">'-b'</span>, <span style="color: #483d8b;">'--bucket'</span><span style="color: black;">&#41;</span>:
            bucket_name = a
        <span style="color: #ff7700;font-weight:bold;">if</span> o <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: black;">&#40;</span><span style="color: #483d8b;">'-d'</span>, <span style="color: #483d8b;">'--debug'</span><span style="color: black;">&#41;</span>:
            debug = <span style="color: #008000;">int</span><span style="color: black;">&#40;</span>a<span style="color: black;">&#41;</span>
        <span style="color: #ff7700;font-weight:bold;">if</span> o <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: black;">&#40;</span><span style="color: #483d8b;">'-n'</span>, <span style="color: #483d8b;">'--no_op'</span><span style="color: black;">&#41;</span>:
            no_op = <span style="color: #008000;">True</span>
        <span style="color: #ff7700;font-weight:bold;">if</span> o <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: black;">&#40;</span><span style="color: #483d8b;">'-q'</span>, <span style="color: #483d8b;">'--quiet'</span><span style="color: black;">&#41;</span>:
            quiet = <span style="color: #008000;">True</span>
        <span style="color: #ff7700;font-weight:bold;">if</span> o <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: black;">&#40;</span><span style="color: #483d8b;">'-s'</span>, <span style="color: #483d8b;">'--secret_key'</span><span style="color: black;">&#41;</span>:
            aws_secret_access_key = a
    <span style="color: #ff7700;font-weight:bold;">if</span> <span style="color: #008000;">len</span><span style="color: black;">&#40;</span>args<span style="color: black;">&#41;</span> <span style="color: #66cc66;">!</span>= <span style="color: #ff4500;">1</span>:
        usage<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
    path = args<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span>
    <span style="color: #ff7700;font-weight:bold;">if</span> bucket_name:
        c = boto.<span style="color: black;">connect_s3</span><span style="color: black;">&#40;</span>aws_access_key_id=aws_access_key_id,
                            aws_secret_access_key=aws_secret_access_key<span style="color: black;">&#41;</span>
        c.<span style="color: black;">debug</span> = debug
        b = c.<span style="color: black;">get_bucket</span><span style="color: black;">&#40;</span>bucket_name<span style="color: black;">&#41;</span>
        keys = <span style="color: black;">&#91;</span><span style="color: black;">&#93;</span>
        <span style="color: #808080; font-style: italic;">#a folder </span>
        <span style="color: #ff7700;font-weight:bold;">if</span> path.<span style="color: black;">endswith</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">"/"</span><span style="color: black;">&#41;</span> <span style="color: #ff7700;font-weight:bold;">and</span> b.<span style="color: black;">get_key</span><span style="color: black;">&#40;</span>path<span style="color: black;">&#41;</span> <span style="color: #ff7700;font-weight:bold;">is</span> <span style="color: #008000;">None</span>:
            <span style="color: #ff7700;font-weight:bold;">for</span> key <span style="color: #ff7700;font-weight:bold;">in</span> b.<span style="color: #008000;">list</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>:
                <span style="color: #ff7700;font-weight:bold;">if</span> key.<span style="color: black;">name</span>.<span style="color: black;">startswith</span><span style="color: black;">&#40;</span>path<span style="color: black;">&#41;</span>:
                    keys.<span style="color: black;">append</span><span style="color: black;">&#40;</span>key.<span style="color: black;">name</span><span style="color: black;">&#41;</span>
        <span style="color: #ff7700;font-weight:bold;">else</span>:
            keys = <span style="color: black;">&#91;</span>path<span style="color: black;">&#93;</span>
        <span style="color: #ff7700;font-weight:bold;">for</span> key <span style="color: #ff7700;font-weight:bold;">in</span> keys:
            <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"remove "</span> + key
            <span style="color: #ff7700;font-weight:bold;">if</span> <span style="color: #ff7700;font-weight:bold;">not</span> no_op:
                b.<span style="color: black;">delete_key</span><span style="color: black;">&#40;</span>key<span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">else</span>:
        usage<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">if</span> __name__ == <span style="color: #483d8b;">"__main__"</span>:
    main<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span></pre>
  </div>
</div>

 [1]: http://blog.ftao.org/2010/05/26/backup-file-on-amazon-s3-using-python-and-boto/
