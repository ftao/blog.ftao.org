---
title: Python 版穿梭程序
author: Filia.Tao
layout: post
permalink: /2007/08/31/py-datapipe/
categories:
  - python
tags:
  - datapipe
  - python
  - 穿梭
---
废话不说,贴代码. 这个版本使用select, 没有使用多线程. 使用多线程的还要简单一点.

<div class="wp_syntax">
  <table>
    <tr>
      <td class="line_numbers">
        <pre>1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
</pre>
      </td>
      
      <td class="code">
        <pre class="python" style="font-family:monospace;"><span style="color: #808080; font-style: italic;">#   datapipe.py</span>
<span style="color: #808080; font-style: italic;">#   Copyright (C) 2007 Filia Tao</span>
<span style="color: #808080; font-style: italic;">#</span>
<span style="color: #808080; font-style: italic;">#   This program is free software; you can redistribute it and/or modify</span>
<span style="color: #808080; font-style: italic;">#   it under the terms of the GNU General Public License as published by</span>
<span style="color: #808080; font-style: italic;">#   the Free Software Foundation; either version 2 of the License, or</span>
<span style="color: #808080; font-style: italic;">#   (at your option) any later version.</span>
<span style="color: #808080; font-style: italic;">#</span>
<span style="color: #808080; font-style: italic;">#   This program is distributed in the hope that it will be useful,</span>
<span style="color: #808080; font-style: italic;">#   but WITHOUT ANY WARRANTY; without even the implied warranty of</span>
<span style="color: #808080; font-style: italic;">#   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the</span>
<span style="color: #808080; font-style: italic;">#   GNU General Public License for more details.</span>
<span style="color: #808080; font-style: italic;">#</span>
<span style="color: #808080; font-style: italic;">#   You should have received a copy of the GNU General Public License along</span>
<span style="color: #808080; font-style: italic;">#   with this program; if not, write to the Free Software Foundation, Inc.,</span>
<span style="color: #808080; font-style: italic;">#   51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">sys</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">socket</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">SocketServer</span>
<span style="color: #ff7700;font-weight:bold;">from</span> <span style="color: #dc143c;">select</span> <span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">select</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">traceback</span>
sample_config = <span style="color: black;">&#123;</span>
    <span style="color: #483d8b;">"server_addr"</span>:<span style="color: black;">&#40;</span><span style="color: #483d8b;">""</span>,<span style="color: #ff4500;">2323</span><span style="color: black;">&#41;</span>,
    <span style="color: #483d8b;">"remote_addr"</span>:<span style="color: black;">&#40;</span><span style="color: #483d8b;">"58.192.114.8"</span>,<span style="color: #ff4500;">23</span><span style="color: black;">&#41;</span>
<span style="color: black;">&#125;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">class</span> datapipeServer<span style="color: black;">&#40;</span><span style="color: #dc143c;">SocketServer</span>.<span style="color: black;">TCPServer</span><span style="color: black;">&#41;</span>:
    <span style="color: #ff7700;font-weight:bold;">def</span> <span style="color: #0000cd;">__init__</span><span style="color: black;">&#40;</span><span style="color: #008000;">self</span>,config<span style="color: black;">&#41;</span>:
        <span style="color: #dc143c;">SocketServer</span>.<span style="color: black;">TCPServer</span>.<span style="color: #0000cd;">__init__</span><span style="color: black;">&#40;</span><span style="color: #008000;">self</span>,config<span style="color: black;">&#91;</span><span style="color: #483d8b;">"server_addr"</span><span style="color: black;">&#93;</span>,<span style="color: #008000;">None</span><span style="color: black;">&#41;</span>
        <span style="color: #008000;">self</span>.<span style="color: black;">config</span> = config
        <span style="color: #008000;">self</span>.<span style="color: black;">in_sockets</span> = <span style="color: black;">&#91;</span><span style="color: black;">&#93;</span>
        <span style="color: #008000;">self</span>.<span style="color: black;">out_sockets</span> = <span style="color: black;">&#91;</span><span style="color: black;">&#93;</span>
        <span style="color: #008000;">self</span>.<span style="color: black;">pair</span> = <span style="color: black;">&#123;</span><span style="color: black;">&#125;</span>
&nbsp;
    <span style="color: #ff7700;font-weight:bold;">def</span> serve_forever<span style="color: black;">&#40;</span><span style="color: #008000;">self</span><span style="color: black;">&#41;</span>:
        <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #008000;">self</span>.<span style="color: black;">config</span>
        <span style="color: #ff7700;font-weight:bold;">while</span> <span style="color: #008000;">True</span>:
            <span style="color: #ff7700;font-weight:bold;">try</span>:
                tri = <span style="color: #dc143c;">select</span><span style="color: black;">&#40;</span><span style="color: black;">&#91;</span><span style="color: #008000;">self</span>.<span style="color: #dc143c;">socket</span><span style="color: black;">&#93;</span> + <span style="color: #008000;">self</span>.<span style="color: black;">in_sockets</span> + <span style="color: #008000;">self</span>.<span style="color: black;">out_sockets</span>,<span style="color: black;">&#91;</span><span style="color: black;">&#93;</span>,<span style="color: black;">&#91;</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>
                <span style="color: #ff7700;font-weight:bold;">for</span> s <span style="color: #ff7700;font-weight:bold;">in</span> tri<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span>:
                    <span style="color: #ff7700;font-weight:bold;">if</span> s <span style="color: #ff7700;font-weight:bold;">is</span> <span style="color: #008000;">self</span>.<span style="color: #dc143c;">socket</span>:
                        ins,client_addr = <span style="color: #008000;">self</span>.<span style="color: black;">get_request</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
                        <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"handle client "</span> + <span style="color: #008000;">str</span><span style="color: black;">&#40;</span>client_addr<span style="color: black;">&#41;</span>
                        outs = <span style="color: #dc143c;">socket</span>.<span style="color: #dc143c;">socket</span><span style="color: black;">&#40;</span><span style="color: #dc143c;">socket</span>.<span style="color: black;">AF_INET</span>, <span style="color: #dc143c;">socket</span>.<span style="color: black;">SOCK_STREAM</span><span style="color: black;">&#41;</span>
                        outs.<span style="color: black;">bind</span><span style="color: black;">&#40;</span><span style="color: black;">&#40;</span><span style="color: #008000;">self</span>.<span style="color: black;">config</span><span style="color: black;">&#91;</span><span style="color: #483d8b;">"server_addr"</span><span style="color: black;">&#93;</span><span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span>,<span style="color: #ff4500;"></span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>
                        outs.<span style="color: black;">connect</span><span style="color: black;">&#40;</span><span style="color: #008000;">self</span>.<span style="color: black;">config</span><span style="color: black;">&#91;</span><span style="color: #483d8b;">"remote_addr"</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>
                        <span style="color: #008000;">self</span>.<span style="color: black;">pair</span><span style="color: black;">&#91;</span>ins<span style="color: black;">&#93;</span> = outs
                        <span style="color: #008000;">self</span>.<span style="color: black;">pair</span><span style="color: black;">&#91;</span>outs<span style="color: black;">&#93;</span> = ins
                        <span style="color: #008000;">self</span>.<span style="color: black;">in_sockets</span>.<span style="color: black;">append</span><span style="color: black;">&#40;</span>ins<span style="color: black;">&#41;</span>
                        <span style="color: #008000;">self</span>.<span style="color: black;">out_sockets</span>.<span style="color: black;">append</span><span style="color: black;">&#40;</span>outs<span style="color: black;">&#41;</span>
                    <span style="color: #ff7700;font-weight:bold;">elif</span> s <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: #008000;">self</span>.<span style="color: black;">in_sockets</span>:
                        data = s.<span style="color: black;">recv</span><span style="color: black;">&#40;</span><span style="color: #ff4500;">4096</span><span style="color: black;">&#41;</span>
                        <span style="color: #ff7700;font-weight:bold;">if</span><span style="color: black;">&#40;</span><span style="color: #008000;">self</span>.<span style="color: black;">pair</span><span style="color: black;">&#91;</span>s<span style="color: black;">&#93;</span>.<span style="color: black;">send</span><span style="color: black;">&#40;</span>data<span style="color: black;">&#41;</span><span style="color: #66cc66;">&</span>lt<span style="color: #66cc66;">;</span>=<span style="color: #ff4500;"></span><span style="color: black;">&#41;</span>:
                            <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"client "</span> + <span style="color: #008000;">str</span><span style="color: black;">&#40;</span>s.<span style="color: black;">getpeername</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span> + <span style="color: #483d8b;">" disconnected"</span><span style="color: #66cc66;">;</span>
                            <span style="color: #008000;">self</span>.<span style="color: black;">pair</span><span style="color: black;">&#91;</span>s<span style="color: black;">&#93;</span>.<span style="color: black;">close</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
                            s.<span style="color: black;">close</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
                            <span style="color: #008000;">self</span>.<span style="color: black;">in_sockets</span>.<span style="color: black;">remove</span><span style="color: black;">&#40;</span>s<span style="color: black;">&#41;</span>
                            <span style="color: #008000;">self</span>.<span style="color: black;">out_sockets</span>.<span style="color: black;">remove</span><span style="color: black;">&#40;</span><span style="color: #008000;">self</span>.<span style="color: black;">pair</span><span style="color: black;">&#91;</span>s<span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>
&nbsp;
                    <span style="color: #ff7700;font-weight:bold;">elif</span> s <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: #008000;">self</span>.<span style="color: black;">out_sockets</span>:
                        data = s.<span style="color: black;">recv</span><span style="color: black;">&#40;</span><span style="color: #ff4500;">4096</span><span style="color: black;">&#41;</span>
                        <span style="color: #ff7700;font-weight:bold;">if</span><span style="color: black;">&#40;</span><span style="color: #008000;">self</span>.<span style="color: black;">pair</span><span style="color: black;">&#91;</span>s<span style="color: black;">&#93;</span>.<span style="color: black;">send</span><span style="color: black;">&#40;</span>data<span style="color: black;">&#41;</span><span style="color: #66cc66;">&</span>lt<span style="color: #66cc66;">;</span>=<span style="color: #ff4500;"></span><span style="color: black;">&#41;</span>:
                            <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"client "</span> + <span style="color: #008000;">str</span><span style="color: black;">&#40;</span><span style="color: #008000;">self</span>.<span style="color: black;">pair</span><span style="color: black;">&#91;</span>s<span style="color: black;">&#93;</span>.<span style="color: black;">getpeername</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span> + <span style="color: #483d8b;">" disconnected"</span><span style="color: #66cc66;">;</span>
                            <span style="color: #008000;">self</span>.<span style="color: black;">pair</span><span style="color: black;">&#91;</span>s<span style="color: black;">&#93;</span>.<span style="color: black;">close</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
                            s.<span style="color: black;">close</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
                            <span style="color: #008000;">self</span>.<span style="color: black;">out_sockets</span>.<span style="color: black;">remove</span><span style="color: black;">&#40;</span>s<span style="color: black;">&#41;</span>
                            <span style="color: #008000;">self</span>.<span style="color: black;">in_sockets</span>.<span style="color: black;">remove</span><span style="color: black;">&#40;</span><span style="color: #008000;">self</span>.<span style="color: black;">pair</span><span style="color: black;">&#91;</span>s<span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>
&nbsp;
            <span style="color: #ff7700;font-weight:bold;">except</span> <span style="color: #008000;">Exception</span>,e:
                <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"Error Occous"</span>
                <span style="color: #808080; font-style: italic;">#traceback.print_stack()</span>
                <span style="color: #ff7700;font-weight:bold;">print</span> e
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> run<span style="color: black;">&#40;</span>server_class=datapipeServer,
        config=sample_config<span style="color: black;">&#41;</span>:
    <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"start server on "</span> + <span style="color: #008000;">str</span><span style="color: black;">&#40;</span>config<span style="color: black;">&#91;</span><span style="color: #483d8b;">"server_addr"</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>
    datapiped = server_class<span style="color: black;">&#40;</span>config<span style="color: black;">&#41;</span>
    datapiped.<span style="color: black;">serve_forever</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> print_help<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>:
    <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'datapipe.py  A datapipe program (by python@bbs.seu.edu.cn)
usage: python datapipe.py [localhost] localport remotehost remoteport
examples:  python datapipe.py 23 bbs.seu.edu.cn 23
           python datapipe.py 10.22.0.2 2323 58.192.114.8 23
'</span><span style="color: #483d8b;">''</span>
<span style="color: #ff7700;font-weight:bold;">if</span> __name__ == <span style="color: #483d8b;">"__main__"</span>:
    <span style="color: #ff7700;font-weight:bold;">if</span> <span style="color: #008000;">len</span><span style="color: black;">&#40;</span><span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#41;</span> <span style="color: #66cc66;">&</span>lt<span style="color: #66cc66;">;</span> <span style="color: #ff4500;">4</span>:
        print_help<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
        <span style="color: #dc143c;">sys</span>.<span style="color: black;">exit</span><span style="color: black;">&#40;</span><span style="color: #ff4500;"></span><span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">elif</span> <span style="color: #008000;">len</span><span style="color: black;">&#40;</span><span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#41;</span> == <span style="color: #ff4500;">4</span>:
        argv = <span style="color: black;">&#91;</span><span style="color: #483d8b;">""</span><span style="color: black;">&#93;</span> + <span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span>:<span style="color: black;">&#93;</span>
    <span style="color: #ff7700;font-weight:bold;">else</span>:
        argv = <span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span>:<span style="color: black;">&#93;</span>
    <span style="color: #808080; font-style: italic;">#print argv</span>
    myconfig = <span style="color: black;">&#123;</span>
        <span style="color: #483d8b;">"server_addr"</span>:<span style="color: black;">&#40;</span>argv<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span>,<span style="color: #008000;">int</span><span style="color: black;">&#40;</span>argv<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>,
        <span style="color: #483d8b;">"remote_addr"</span>:<span style="color: black;">&#40;</span>argv<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span>,<span style="color: #008000;">int</span><span style="color: black;">&#40;</span>argv<span style="color: black;">&#91;</span><span style="color: #ff4500;">3</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>,
    <span style="color: black;">&#125;</span>
    run<span style="color: black;">&#40;</span>config = myconfig<span style="color: black;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>
