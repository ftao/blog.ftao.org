---
title: 使用Python 和Boto 库将文件备份到AmazonS3
author: Filia.Tao
layout: post
permalink: /2010/05/26/backup-file-on-amazon-s3-using-python-and-boto/
categories:
  - python
  - 编程开发
tags:
  - amazon
  - boto
  - python
  - s3
---
AmazonS3 是一个很好用的云存储服务。 够便宜。 速度也不错。是个备份的好地方。  
Boto 是Amazon WebService （包含S3)的Python绑定， 很好用。 （唯一的问题是稍微有一点滞后，比如S3现在已经有了在新加坡的区域。这个还没有更新）  
<a href="http://code.google.com/p/boto/" target="_blank">Boto</a> 提供一个很实用的命令行工具s3put, 可以很方便的将一个目录/文件存储到S3上去。  
比如下面的脚本将/home/ftao/backup/daily 下面的文件保存在ftaobackup 这个bucket 上面去。

<div class="wp_syntax">
  <div class="code">
    <pre class="bash" style="font-family:monospace;"><span style="color: #666666; font-style: italic;">#!/bin/sh</span>
<span style="color: #7a0874; font-weight: bold;">export</span> <span style="color: #007800;">AWS_ACCESS_KEY_ID</span>=YOUR-AWS-ACCESS-KEY-ID
<span style="color: #7a0874; font-weight: bold;">export</span> <span style="color: #007800;">AWS_SECRET_ACCESS_KEY</span>=YOUR-AWS-SECRET-ACCESS-KEY
<span style="color: #007800;">BUCKET_NAME</span>=ftaobackup
<span style="color: #000000; font-weight: bold;">/</span>home<span style="color: #000000; font-weight: bold;">/</span>ftao<span style="color: #000000; font-weight: bold;">/</span>env<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span>s3put <span style="color: #660033;">-b</span> <span style="color: #007800;">$BUCKET_NAME</span> --no_overwrite <span style="color: #660033;">-p</span> <span style="color: #000000; font-weight: bold;">/</span>home<span style="color: #000000; font-weight: bold;">/</span>ftao<span style="color: #000000; font-weight: bold;">/</span>backup<span style="color: #000000; font-weight: bold;">/</span>  <span style="color: #000000; font-weight: bold;">/</span>home<span style="color: #000000; font-weight: bold;">/</span>ftao<span style="color: #000000; font-weight: bold;">/</span>backup<span style="color: #000000; font-weight: bold;">/</span>daily</pre>
  </div>
</div>

在给出一个创建bucket 的代码例子。

<div class="wp_syntax">
  <div class="code">
    <pre class="python" style="font-family:monospace;"><span style="color: #ff7700;font-weight:bold;">def</span> create_or_get_bucket<span style="color: black;">&#40;</span>bucket_name, location<span style="color: black;">&#41;</span>:
    <span style="color: #ff7700;font-weight:bold;">import</span> boto
    <span style="color: #ff7700;font-weight:bold;">from</span> boto.<span style="color: black;">exception</span> <span style="color: #ff7700;font-weight:bold;">import</span> S3CreateError
    conn = boto.<span style="color: black;">connect_s3</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
    bucket = conn.<span style="color: black;">lookup</span><span style="color: black;">&#40;</span>bucket_name<span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">if</span> bucket <span style="color: #ff7700;font-weight:bold;">is</span> <span style="color: #008000;">None</span>:
        bucket = conn.<span style="color: black;">create_bucket</span><span style="color: black;">&#40;</span>bucket_name, <span style="color: #008000;">None</span>, location<span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">else</span>:
        <span style="color: #ff7700;font-weight:bold;">if</span> bucket.<span style="color: black;">get_location</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span> <span style="color: #66cc66;">!</span>= location:
            <span style="color: #ff7700;font-weight:bold;">raise</span> <span style="color: #008000;">Exception</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'bad bucket location'</span><span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">return</span> bucket
&nbsp;
<span style="color: #ff7700;font-weight:bold;">if</span> __name__ == <span style="color: #483d8b;">"__main__"</span>:
    <span style="color: #ff7700;font-weight:bold;">from</span> boto.<span style="color: black;">s3</span>.<span style="color: black;">connection</span> <span style="color: #ff7700;font-weight:bold;">import</span> Location
    bucket = create_or_get_bucket<span style="color: black;">&#40;</span><span style="color: #483d8b;">'ftaobackup'</span>, <span style="color: #483d8b;">"us-west-1"</span><span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">print</span> bucket</pre>
  </div>
</div>