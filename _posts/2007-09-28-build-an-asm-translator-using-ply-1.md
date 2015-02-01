---
title: 使用PLY 构建一个汇编器(1)
author: Filia.Tao
layout: post
permalink: /2007/09/28/build-an-asm-translator-using-ply-1/
categories:
  - compiler
  - python
tags:
  - asm
  - ply
  - python
  - 汇编器
---
PLY 是一个Python 的Lex/YACC 实现. 官方网站在这里<a href="http://www.dabeaz.com/ply/" title="An implementation of lex and yacc parsing tools for Python" target="_blank">http://www.dabeaz.com/ply/</a>  
这学期有一门课叫 ” 计算机综合课程设计-基于MIPS16的SOC设计实践” ,其中有一部分是实现一个汇编器. 将MIPS汇编程序转换成Quartus 的mif 文件.

这样的需求使用Lex/YACC 最合适不过, 本来是想使用Flex/Bison , 或上学期自己实现的<a href="http://blog.ftao.org/category/computer-science/compiler/lex/" title="我写的关于SEULex 的一些文章" target="_blank">lex</a>和yacc. 但是在尝试了一下后,决定放弃.原因在于C/C++ 对文本的操纵比较麻烦,另外yacc 的语义动作限制比较大. (主要是数据类型问题)

而PLY 这个实现学习成本很低, 而且很Pythonic.加入符号表等等功能也很简单.  
首先我们先来看我们要处理的汇编文件的格式.

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
</pre>
      </td>
      
      <td class="code">
        <pre class="asm" style="font-family:monospace;">ORG_DATA  <span style="color: #0000ff;">0000</span>           <span style="color: #666666; font-style: italic;">;The start address of data</span>
BUF   <span style="color: #000000; font-weight: bold;">DW</span>  00FF<span style="color: #339933;">,</span> <span style="color: #0000ff;">5500</span>
NEXT  <span style="color: #000000; font-weight: bold;">DW</span>  0CFF<span style="color: #339933;">,</span> 5D00
ORG_CODE  <span style="color: #0000ff;">0000</span>		       <span style="color: #666666; font-style: italic;">;The start address of code</span>
start<span style="color: #339933;">:</span>  addi  $t0<span style="color: #339933;">,</span> $Zero<span style="color: #339933;">,</span> <span style="color: #0000ff;"></span>     <span style="color: #666666; font-style: italic;">;A label for the first statement,$t0=0</span>
 	lw    $v0<span style="color: #339933;">,</span> BUF<span style="color: #009900; font-weight: bold;">&#40;</span>$t0<span style="color: #009900; font-weight: bold;">&#41;</span>      <span style="color: #666666; font-style: italic;">;$v0=00FF  (BUF[0])</span>
 	addi  $t0<span style="color: #339933;">,</span> $t0<span style="color: #339933;">,</span> <span style="color: #0000ff;">2</span>
 	lw	  $v1<span style="color: #339933;">,</span> BUF<span style="color: #009900; font-weight: bold;">&#40;</span>$t0<span style="color: #009900; font-weight: bold;">&#41;</span>	 <span style="color: #666666; font-style: italic;">;$v1=5500  (BUF[2])</span>
 	<span style="color: #00007f; font-weight: bold;">add</span>   $v0<span style="color: #339933;">,</span> $v0<span style="color: #339933;">,</span> $v1	<span style="color: #666666; font-style: italic;">;$v0=$v0+$v1=55FF</span>
 	addi  $t0<span style="color: #339933;">,</span> $t0<span style="color: #339933;">,</span> <span style="color: #0000ff;">2</span>
 	sw    $v0<span style="color: #339933;">,</span> BUF<span style="color: #009900; font-weight: bold;">&#40;</span>$t0<span style="color: #009900; font-weight: bold;">&#41;</span>    	<span style="color: #666666; font-style: italic;">;BUF[4]=55FF</span>
 	j	  start
<span style="color: #000000; font-weight: bold;">end</span>  start</pre>
      </td>
    </tr>
  </table>
</div>

下面我们来书写汇编文件的lex 定义. token 类型大概有下面这些.

  1. MIPS 基本指令 (课程设计的要求是31条)
  2. 若干伪指令 (很少, 主要有ORG\_DATA , ORG\_CODE 等)
  3. 符号地址 / 标号
  4. 注释
  5. 立即数
  6. 地址
  7. 其他的符号 (逗号,括号, 冒号等)

PLY 的lex 文件格式其实就是一个标准的Python 文件, 不过使用一些规定的格式/名字来指定.  
(下面的代码比较长, 所以我要用&#8211;MORE&#8211;了)  
<!--more-->

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
</pre>
      </td>
      
      <td class="code">
        <pre class="python" style="font-family:monospace;">tokens =  <span style="color: black;">&#91;</span>
	<span style="color: #483d8b;">'IDENTIFIER'</span>, <span style="color: #483d8b;">'NUMBER'</span>,<span style="color: #483d8b;">'REG'</span>,
	<span style="color: #483d8b;">'ORG_DATA'</span>, <span style="color: #483d8b;">'ORG_CODE'</span>, <span style="color: #483d8b;">'DW'</span>, <span style="color: #483d8b;">'END'</span>,
	<span style="color: #483d8b;">'ADD'</span>, <span style="color: #483d8b;">'ADDU'</span>, <span style="color: #483d8b;">'SUB'</span>, <span style="color: #483d8b;">'SUBU'</span>, <span style="color: #483d8b;">'AND'</span>, <span style="color: #483d8b;">'OR'</span>, <span style="color: #483d8b;">'XOR'</span>, <span style="color: #483d8b;">'NOR'</span>, <span style="color: #483d8b;">'SLT'</span>, <span style="color: #483d8b;">'SLTU'</span>,
	<span style="color: #483d8b;">'SLL'</span>, <span style="color: #483d8b;">'SRL'</span>, <span style="color: #483d8b;">'SRA'</span>,
	<span style="color: #483d8b;">'SLLV'</span>, <span style="color: #483d8b;">'SRLV'</span>, <span style="color: #483d8b;">'SRAV'</span>,
	<span style="color: #483d8b;">'JR'</span>,
	<span style="color: #483d8b;">'ADDI'</span>,<span style="color: #483d8b;">'ADDIU'</span>,<span style="color: #483d8b;">'ANDI'</span>,<span style="color: #483d8b;">'ORI'</span>, <span style="color: #483d8b;">'XORI'</span>,
	<span style="color: #483d8b;">'LUI'</span>,
	<span style="color: #483d8b;">'LW'</span>, <span style="color: #483d8b;">'SW'</span>,
	<span style="color: #483d8b;">'BEQ'</span>, <span style="color: #483d8b;">'BNE'</span>, <span style="color: #483d8b;">'SLTI'</span>, <span style="color: #483d8b;">'SLTIU'</span>,
	<span style="color: #483d8b;">'J'</span>, <span style="color: #483d8b;">'JAL'</span><span style="color: black;">&#93;</span>
&nbsp;
literals = <span style="color: black;">&#91;</span> <span style="color: #483d8b;">':'</span>,<span style="color: #483d8b;">','</span>,<span style="color: #483d8b;">'('</span>,<span style="color: #483d8b;">')'</span> <span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> t_NUMBER<span style="color: black;">&#40;</span>t<span style="color: black;">&#41;</span>:
	r<span style="color: #483d8b;">'[0-9][a-fA-F0-9]*'</span>
	<span style="color: #ff7700;font-weight:bold;">try</span>:
		t.<span style="color: black;">value</span> = <span style="color: #008000;">int</span><span style="color: black;">&#40;</span>t.<span style="color: black;">value</span>,<span style="color: #ff4500;">16</span><span style="color: black;">&#41;</span><span style="color: #66cc66;">;</span>
	<span style="color: #ff7700;font-weight:bold;">except</span> <span style="color: #008000;">ValueError</span>:
		<span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"Number %s is bad!"</span> <span style="color: #66cc66;">%</span> t.<span style="color: black;">value</span>
		t.<span style="color: black;">value</span> = <span style="color: #ff4500;"></span>
	<span style="color: #ff7700;font-weight:bold;">return</span> t		
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> t_COMMENT<span style="color: black;">&#40;</span>t<span style="color: black;">&#41;</span>:
	r<span style="color: #483d8b;">';.*'</span>
	<span style="color: #ff7700;font-weight:bold;">pass</span>
&nbsp;
reserved  = <span style="color: black;">&#123;</span>
	<span style="color: #483d8b;">"add"</span>		:<span style="color: #483d8b;">"ADD"</span>,
	<span style="color: #483d8b;">"addu"</span>		:<span style="color: #483d8b;">"ADDU"</span>,
	<span style="color: #483d8b;">"sub"</span>		:<span style="color: #483d8b;">"SUB"</span>,
	<span style="color: #483d8b;">"subu"</span>		:<span style="color: #483d8b;">"SUBU"</span>,
	<span style="color: #483d8b;">"and"</span>		:<span style="color: #483d8b;">"AND"</span>,
	<span style="color: #483d8b;">"or"</span>		:<span style="color: #483d8b;">"OR"</span>,
	<span style="color: #483d8b;">"xor"</span>		:<span style="color: #483d8b;">"XOR"</span>,
	<span style="color: #483d8b;">"nor"</span>		:<span style="color: #483d8b;">"NOR"</span>,
	<span style="color: #483d8b;">"slt"</span>		:<span style="color: #483d8b;">"SLT"</span>,
	<span style="color: #483d8b;">"sltu"</span>		:<span style="color: #483d8b;">"SLTU"</span>,
	<span style="color: #483d8b;">"sll"</span>		:<span style="color: #483d8b;">"SLL"</span>,
	<span style="color: #483d8b;">"srl"</span>		:<span style="color: #483d8b;">"SRL"</span>,
	<span style="color: #483d8b;">"sra"</span>		:<span style="color: #483d8b;">"SRA"</span>,
	<span style="color: #483d8b;">"sllv"</span>		:<span style="color: #483d8b;">"SLLV"</span>,
	<span style="color: #483d8b;">"srlv"</span>		:<span style="color: #483d8b;">"SRLV"</span>,
	<span style="color: #483d8b;">"srav"</span>		:<span style="color: #483d8b;">"SRAV"</span>,
	<span style="color: #483d8b;">"jr"</span>		:<span style="color: #483d8b;">"JR"</span>,
	<span style="color: #483d8b;">"addi"</span>		:<span style="color: #483d8b;">"ADDI"</span>,
	<span style="color: #483d8b;">"addiu"</span>		:<span style="color: #483d8b;">"ADDIU"</span>,
	<span style="color: #483d8b;">"ori"</span>		:<span style="color: #483d8b;">"ORI"</span>,
	<span style="color: #483d8b;">"xori"</span>		:<span style="color: #483d8b;">"XORI"</span>,
	<span style="color: #483d8b;">"lui"</span>		:<span style="color: #483d8b;">"LUI"</span>,
	<span style="color: #483d8b;">"lw"</span>		:<span style="color: #483d8b;">"LW"</span>,
	<span style="color: #483d8b;">"sw"</span>		:<span style="color: #483d8b;">"SW"</span>,
	<span style="color: #483d8b;">"beq"</span>		:<span style="color: #483d8b;">"BEQ"</span>,
	<span style="color: #483d8b;">"bne"</span>		:<span style="color: #483d8b;">"BNE"</span>,
	<span style="color: #483d8b;">"slti"</span>		:<span style="color: #483d8b;">"SLTI"</span>,
	<span style="color: #483d8b;">"sltiu"</span>		:<span style="color: #483d8b;">"SLTIU"</span>,
	<span style="color: #483d8b;">"j"</span>			:<span style="color: #483d8b;">"J"</span>,
	<span style="color: #483d8b;">"jal"</span>		:<span style="color: #483d8b;">"JAL"</span>,
	<span style="color: #483d8b;">"ORG_DATA"</span> 	:<span style="color: #483d8b;">"ORG_DATA"</span>,
	<span style="color: #483d8b;">"ORG_CODE"</span> 	:<span style="color: #483d8b;">"ORG_CODE"</span>,
	<span style="color: #483d8b;">"DW"</span> 		:<span style="color: #483d8b;">"DW"</span>,
	<span style="color: #483d8b;">"end"</span>		:<span style="color: #483d8b;">"END"</span>,
<span style="color: black;">&#125;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> t_IDENTIFIER<span style="color: black;">&#40;</span>t<span style="color: black;">&#41;</span>:
	r<span style="color: #483d8b;">'<span style="color: #000099; font-weight: bold;">\b</span>[a-zA-Z_][a-zA-Z_0-9]*'</span>
	t.<span style="color: #008000;">type</span> = reserved.<span style="color: black;">get</span><span style="color: black;">&#40;</span>t.<span style="color: black;">value</span>,<span style="color: #483d8b;">'IDENTIFIER'</span><span style="color: black;">&#41;</span>    <span style="color: #808080; font-style: italic;"># Check for reserved words</span>
	<span style="color: #ff7700;font-weight:bold;">return</span> t	
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> t_REG<span style="color: black;">&#40;</span>t<span style="color: black;">&#41;</span>:
	r<span style="color: #483d8b;">'<span style="color: #000099; font-weight: bold;">\$</span>[a-zA-Z_0-9]*'</span>
	<span style="color: #ff7700;font-weight:bold;">return</span> t
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> t_newline<span style="color: black;">&#40;</span>t<span style="color: black;">&#41;</span>:
	r<span style="color: #483d8b;">'<span style="color: #000099; font-weight: bold;">\n</span>+'</span>
	t.<span style="color: black;">lexer</span>.<span style="color: black;">lineno</span> += <span style="color: #008000;">len</span><span style="color: black;">&#40;</span>t.<span style="color: black;">value</span><span style="color: black;">&#41;</span>
&nbsp;
t_ignore  = <span style="color: #483d8b;">' <span style="color: #000099; font-weight: bold;">\t</span><span style="color: #000099; font-weight: bold;">\v</span>'</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> t_error<span style="color: black;">&#40;</span>t<span style="color: black;">&#41;</span>:
    <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"Illegal character '%s' on line %d "</span> <span style="color: #66cc66;">%</span> <span style="color: black;">&#40;</span>t.<span style="color: black;">value</span><span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span>,t.<span style="color: black;">lexer</span>.<span style="color: black;">lineno</span><span style="color: black;">&#41;</span>
    t.<span style="color: black;">lexer</span>.<span style="color: black;">skip</span><span style="color: black;">&#40;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #808080; font-style: italic;"># Compute column.</span>
<span style="color: #808080; font-style: italic;">#     input is the input text string</span>
<span style="color: #808080; font-style: italic;">#     token is a token instance</span>
<span style="color: #ff7700;font-weight:bold;">def</span> find_column<span style="color: black;">&#40;</span><span style="color: #008000;">input</span>,<span style="color: #dc143c;">token</span><span style="color: black;">&#41;</span>:
    i = <span style="color: #dc143c;">token</span>.<span style="color: black;">lexpos</span>
    <span style="color: #ff7700;font-weight:bold;">while</span> i <span style="color: #66cc66;">&</span>gt<span style="color: #66cc66;">;</span> <span style="color: #ff4500;"></span>:
        <span style="color: #ff7700;font-weight:bold;">if</span> <span style="color: #008000;">input</span><span style="color: black;">&#91;</span>i<span style="color: black;">&#93;</span> == <span style="color: #483d8b;">'<span style="color: #000099; font-weight: bold;">\n</span>'</span>: <span style="color: #ff7700;font-weight:bold;">break</span>
        i -= <span style="color: #ff4500;">1</span>
    column = <span style="color: black;">&#40;</span><span style="color: #dc143c;">token</span>.<span style="color: black;">lexpos</span> - i<span style="color: black;">&#41;</span>+<span style="color: #ff4500;">1</span>
    <span style="color: #ff7700;font-weight:bold;">return</span> column</pre>
      </td>
    </tr>
  </table>
</div>

所有以t_ 开头的函数/变量 都是 PLY 使用的token 定义, PLY 使用Python 文档字符串来表示token对应的正则表达式, 而函数的返回值就是这个token的值. (更对关于PLY 的信息,请查看<a href="http://www.dabeaz.com/ply/ply.html" target="_blank">文档</a>)  
需要注意的是PLY (直接或间接的)支持记录token 的行号/列号, 这样就可以很方便进行错误报告. (注意t_error 函数)
