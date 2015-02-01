---
title: 使用PLY 构建一个汇编器(2)
author: Filia.Tao
layout: post
permalink: /2007/09/29/build-an-asm-translator-using-ply-2/
categories:
  - compiler
  - python
tags:
  - compiler
  - python
---
Lex 和Yacc 总是需要合作来完成任务. Lex 返回token , Yacc 根据产生式来生成语法分析器.  
在PLY 中可以非常容易的书写语义动作, 完成我们的任务. 类似 lex, yacc 也使用以”p_”开头的函数(文档字符串表示产生式), 函数的内容就是语义动作.根据我们的目标, 我们需要对输入文件做三次扫描.

  1. 翻译. 
      * 将数据/指令翻译成mif 文件的格式 , 这里我们使用16 进制.
      * 建立符号表
      * 对于翻译时还没有得到地址的符号名, 使用类似{BUF_16} 的字符串表示. (16位的BUF变量)
  2. 符号替换 
      * 将类似{BUF_16}的记号 用符号表中的地址替换
      * 将2进制转换成16进制
  3. 添加注释 
      * 将原来文件的内容放在 翻译后的代码后面. (一行对应一行)

下面是代码, 比较长. 而且有一些Ugly.  
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
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
135
136
137
138
139
140
141
142
143
144
145
146
147
148
149
150
151
152
153
154
155
156
157
158
159
160
161
162
163
164
165
166
167
168
169
170
171
172
173
174
175
176
177
178
179
180
181
182
183
184
185
186
187
188
189
190
191
192
193
194
195
196
197
198
199
200
201
202
203
204
205
206
207
208
209
210
211
212
213
214
215
216
217
218
219
220
221
222
223
224
225
226
227
228
229
230
231
232
233
234
235
236
237
238
239
240
241
242
243
244
245
246
247
248
249
250
251
252
253
254
255
256
257
258
259
260
261
262
263
264
265
266
267
268
269
270
271
272
273
274
275
276
277
278
279
280
281
282
283
284
285
286
287
288
289
290
291
292
293
294
295
296
297
298
299
300
301
302
303
304
305
306
307
308
309
310
311
312
313
314
315
316
317
318
319
320
321
322
323
324
325
326
327
328
329
</pre>
      </td>
      
      <td class="code">
        <pre class="python" style="font-family:monospace;"><span style="color: #808080; font-style: italic;">#vim:filencoding=utf-8</span>
<span style="color: #ff7700;font-weight:bold;">from</span> ply <span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #66cc66;">*</span>
<span style="color: #ff7700;font-weight:bold;">from</span> asmlex <span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #66cc66;">*</span>
<span style="color: #ff7700;font-weight:bold;">from</span> asmmapping <span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #66cc66;">*</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">re</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">sys</span>
&nbsp;
<span style="color: #808080; font-style: italic;">#本程序相关的变量</span>
&nbsp;
start = <span style="color: #483d8b;">'asm_source'</span>
&nbsp;
tokens =  <span style="color: black;">&#91;</span><span style="color: #483d8b;">'IDENTIFIER'</span>, <span style="color: #483d8b;">'NUMBER'</span>, <span style="color: #483d8b;">'COMMENT'</span>, <span style="color: #483d8b;">'REG'</span>, <span style="color: #483d8b;">'ORG_DATA'</span>, <span style="color: #483d8b;">'ORG_CODE'</span>, <span style="color: #483d8b;">'DW'</span>, <span style="color: #483d8b;">'END'</span>, 
           <span style="color: #483d8b;">'ADD'</span>, <span style="color: #483d8b;">'ADDU'</span>, <span style="color: #483d8b;">'SUB'</span>, <span style="color: #483d8b;">'SUBU'</span>, <span style="color: #483d8b;">'AND'</span>, <span style="color: #483d8b;">'OR'</span>, <span style="color: #483d8b;">'XOR'</span>, <span style="color: #483d8b;">'NOR'</span>, 
           <span style="color: #483d8b;">'SLT'</span>, <span style="color: #483d8b;">'SLTU'</span>, <span style="color: #483d8b;">'SLL'</span>, <span style="color: #483d8b;">'SRL'</span>, <span style="color: #483d8b;">'SRA'</span>, <span style="color: #483d8b;">'SLLV'</span>, <span style="color: #483d8b;">'SRLV'</span>, <span style="color: #483d8b;">'SRAV'</span>, 
           <span style="color: #483d8b;">'JR'</span>, <span style="color: #483d8b;">'ADDI'</span>, <span style="color: #483d8b;">'ADDIU'</span>, <span style="color: #483d8b;">'ORI'</span>, <span style="color: #483d8b;">'XORI'</span>, <span style="color: #483d8b;">'LUI'</span>, <span style="color: #483d8b;">'LW'</span>, <span style="color: #483d8b;">'SW'</span>, 
           <span style="color: #483d8b;">'BEQ'</span>, <span style="color: #483d8b;">'BNE'</span>, <span style="color: #483d8b;">'SLTI'</span>, <span style="color: #483d8b;">'SLTIU'</span>, <span style="color: #483d8b;">'J'</span>, <span style="color: #483d8b;">'JAL'</span><span style="color: black;">&#93;</span>
&nbsp;
precedence =  <span style="color: black;">&#91;</span><span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #808080; font-style: italic;"># -------------- RULES ----------------</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_empty<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'empty :'</span><span style="color: #483d8b;">''</span>
    <span style="color: #808080; font-style: italic;">#p[0] = ""</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_asm_source<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'asm_source : data_block code_block '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span> + <span style="color: #008000;">str</span><span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>
    <span style="color: #808080; font-style: italic;">#print p[0]</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_data_block<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'data_block : org_data_declare data_defines'</span><span style="color: #483d8b;">''</span>
    head = <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'
DEPTH = 1024;  --10 bits of data line, 210＝1024
WIDTH = 16;     -- the width of data is 16 bits
&nbsp;
ADDRESS_RADIX = HEX; -- describe address in hex 
DATA_RADIX = HEX;         -- describe data in hex 
&nbsp;
CONTENT
BEGIN
'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = head + p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span> + <span style="color: #483d8b;">"END;<span style="color: #000099; font-weight: bold;">\n</span>"</span>
    p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">data_file_content</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_org_data_declare<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'org_data_declare : ORG_DATA NUMBER '</span><span style="color: #483d8b;">''</span>
    p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">data_address</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span> <span style="color: #808080; font-style: italic;">#record start address</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_data_defines_1<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'data_defines : data_define_line data_defines 
    '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span> + p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_data_defines_2<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'data_defines : data_define_line'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_data_define_line<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'data_define_line : data_define'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_data_define<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'data_define : IDENTIFIER DW data_list'</span><span style="color: #483d8b;">''</span>
    p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">symbol_table</span><span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span> = p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">data_address</span> 
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">3</span><span style="color: black;">&#93;</span>
&nbsp;
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_data_list_1<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'data_list : data_list  "," NUMBER '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span> + <span style="color: #483d8b;">"%X : %x <span style="color: #000099; font-weight: bold;">\n</span>"</span> <span style="color: #66cc66;">%</span> <span style="color: black;">&#40;</span>p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">data_address</span>/<span style="color: #ff4500;">2</span> , p<span style="color: black;">&#91;</span><span style="color: #ff4500;">3</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>
    p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">data_address</span> = p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">data_address</span> + <span style="color: #ff4500;">2</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_data_list_2<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'data_list : NUMBER'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">"%X : %x <span style="color: #000099; font-weight: bold;">\n</span>"</span> <span style="color: #66cc66;">%</span> <span style="color: black;">&#40;</span>p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">data_address</span>/<span style="color: #ff4500;">2</span> , p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>
    p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">data_address</span> = p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">data_address</span> + <span style="color: #ff4500;">2</span>
&nbsp;
&nbsp;
<span style="color: #808080; font-style: italic;">#########################################################################</span>
<span style="color: #808080; font-style: italic;">#            下面是代码段相关</span>
<span style="color: #808080; font-style: italic;">#########################################################################</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_code_block<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'code_block : org_code_declare code_lines end_declare'</span><span style="color: #483d8b;">''</span>
    head = <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'
DEPTH = 512;       --  9 bits of data line, 29＝512
WIDTH = 32;         -- the width of data is 32 bits
ADDRESS_RADIX = HEX; -- describe address in hex DATA_RADIX = HEX;        -- describe data in hex 
CONTENT
BEGIN
'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = head + p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span> + <span style="color: #483d8b;">"END<span style="color: #000099; font-weight: bold;">\n</span>"</span>
    p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">code_file_content</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_org_code_declare<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'org_code_declare : ORG_CODE NUMBER '</span><span style="color: #483d8b;">''</span>
    p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">code_address</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span> <span style="color: #808080; font-style: italic;">#record start address</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_code_lines_1<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'code_lines : code_line code_lines '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span> + p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_code_lines_2<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'code_lines : code_line '</span><span style="color: #483d8b;">''</span>    
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_code_line<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'code_line : instruction_define'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">"%X : BIN_%s -- {LINE_%d} <span style="color: #000099; font-weight: bold;">\n</span>"</span> <span style="color: #66cc66;">%</span><span style="color: black;">&#40;</span>p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">code_address</span>/<span style="color: #ff4500;">4</span> , p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span> , p.<span style="color: black;">lineno</span><span style="color: black;">&#40;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>  <span style="color: #808080; font-style: italic;"># 注释以后加上,采用替换方法</span>
    p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">code_address</span> = p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">code_address</span> + <span style="color: #ff4500;">4</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_instruction_define_1<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'instruction_define : label_define instruction'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span> 
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_instruction_define_2<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'instruction_define : instruction'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_label_define<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'label_define : IDENTIFIER ":"'</span><span style="color: #483d8b;">''</span>
    p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">symbol_table</span><span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span> = p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">code_address</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span> + p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_instruction<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'instruction  : r_type_ins
                    | s_type_ins
                    | jr_type_ins
                    | i_type_ins
                    | j_type_ins
                    | ls_type_ins
    '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_r_type_ins<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'r_type_ins : r_op REG "," REG "," REG'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">""</span>.<span style="color: black;">join</span><span style="color: black;">&#40;</span>ins_format<span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span> <span style="color: #66cc66;">%</span> <span style="color: black;">&#123;</span>
        <span style="color: #483d8b;">"rd"</span>:enocde_reg<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>,
        <span style="color: #483d8b;">"rs"</span>:enocde_reg<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">4</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>,
        <span style="color: #483d8b;">"rt"</span>:enocde_reg<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">6</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>,
    <span style="color: black;">&#125;</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_s_type_ins<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'s_type_ins : s_op REG "," REG "," NUMBER'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">""</span>.<span style="color: black;">join</span><span style="color: black;">&#40;</span>ins_format<span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span> <span style="color: #66cc66;">%</span> <span style="color: black;">&#123;</span>
        <span style="color: #483d8b;">"rd"</span>:enocde_reg<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>,
        <span style="color: #483d8b;">"rt"</span>:enocde_reg<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">4</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>,
        <span style="color: #483d8b;">"smt"</span>:dec2bin<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">6</span><span style="color: black;">&#93;</span>,<span style="color: #ff4500;">5</span><span style="color: black;">&#41;</span>
    <span style="color: black;">&#125;</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_jr_type_ins<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'jr_type_ins : jr_op REG'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">""</span>.<span style="color: black;">join</span><span style="color: black;">&#40;</span>ins_format<span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span> <span style="color: #66cc66;">%</span> <span style="color: black;">&#123;</span>
        <span style="color: #483d8b;">"rs"</span>:enocde_reg<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>,
    <span style="color: black;">&#125;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_i_type_ins<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'i_type_ins : i_op REG "," REG "," NUMBER'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">""</span>.<span style="color: black;">join</span><span style="color: black;">&#40;</span>ins_format<span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span> <span style="color: #66cc66;">%</span> <span style="color: black;">&#123;</span>
        <span style="color: #483d8b;">"rt"</span>:enocde_reg<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>,
        <span style="color: #483d8b;">"rs"</span>:enocde_reg<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">4</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>,
        <span style="color: #483d8b;">"imm"</span>:dec2bin<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">6</span><span style="color: black;">&#93;</span>,<span style="color: #ff4500;">16</span><span style="color: black;">&#41;</span>,
    <span style="color: black;">&#125;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_ls_type_ins<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'ls_type_ins : ls_op REG "," address16 "(" REG ")"'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">""</span>.<span style="color: black;">join</span><span style="color: black;">&#40;</span>ins_format<span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span> <span style="color: #66cc66;">%</span> <span style="color: black;">&#123;</span>
        <span style="color: #483d8b;">"rt"</span>:enocde_reg<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>,
        <span style="color: #483d8b;">"rs"</span>:enocde_reg<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">6</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>,
        <span style="color: #483d8b;">"imm"</span>:p<span style="color: black;">&#91;</span><span style="color: #ff4500;">4</span><span style="color: black;">&#93;</span> 
    <span style="color: black;">&#125;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_address16_1<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'address16 : NUMBER'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = dec2bin<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>,<span style="color: #ff4500;">16</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_address16_2<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'address16 : IDENTIFIER'</span><span style="color: #483d8b;">''</span>    
    <span style="color: #ff7700;font-weight:bold;">try</span>:
        addr = p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">symbol_table</span><span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span>
        p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = dec2bin<span style="color: black;">&#40;</span>addr,<span style="color: #ff4500;">16</span><span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">except</span>:
        p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">"{%s_16}"</span> <span style="color: #66cc66;">%</span> p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_j_type_ins<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'j_type_ins : j_op address26'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">""</span>.<span style="color: black;">join</span><span style="color: black;">&#40;</span>ins_format<span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span> <span style="color: #66cc66;">%</span> <span style="color: black;">&#123;</span>
        <span style="color: #483d8b;">"add"</span>:p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span>,
    <span style="color: black;">&#125;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_address26_1<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'address26 : IDENTIFIER '</span><span style="color: #483d8b;">''</span>
    <span style="color: #ff7700;font-weight:bold;">try</span>:
        addr = p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">symbol_table</span><span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span>
        p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = dec2bin<span style="color: black;">&#40;</span>addr,<span style="color: #ff4500;">26</span><span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">except</span>:
        p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = <span style="color: #483d8b;">"{%s_26}"</span> <span style="color: #66cc66;">%</span> p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_address26_2<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'address26 : NUMBER '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = dec2bin<span style="color: black;">&#40;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>,<span style="color: #ff4500;">26</span><span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"address 26 "</span> + p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_r_op<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'r_op : ADD 
            | ADDU 
            | SUB 
            | SUBU 
            | AND 
            | OR 
            | XOR 
            | NOR 
            | SLT 
            | SLTU 
            | SLLV 
            | SRLV 
            | SRAV 
    '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>.<span style="color: black;">lower</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_s_op<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'s_op : SLL 
            | SRL 
            | SRA 
    '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>.<span style="color: black;">lower</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_jr_op<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'jr_op : JR'</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>.<span style="color: black;">lower</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_i_op<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'i_op : ADDI 
            | ADDIU 
            | ORI 
            | XORI 
            | LUI 
            | BEQ 
            | BNE 
            | SLTI 
            | SLTIU 
    '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>.<span style="color: black;">lower</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_j_op<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'j_op : J 
            | JAL
    '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>.<span style="color: black;">lower</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_ls_op<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'ls_op : LW 
             | SW
    '</span><span style="color: #483d8b;">''</span>
    p<span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span> = p<span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>.<span style="color: black;">lower</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_end_declare<span style="color: black;">&#40;</span>p<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'end_declare : END IDENTIFIER'</span><span style="color: #483d8b;">''</span>
    <span style="color: #ff7700;font-weight:bold;">try</span>:
        p.<span style="color: #dc143c;">parser</span>.<span style="color: black;">symbol_table</span><span style="color: black;">&#91;</span>p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span><span style="color: black;">&#93;</span>
    <span style="color: #ff7700;font-weight:bold;">except</span>:
        <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"NOT FOUND START LABEL"</span> + p<span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> p_error<span style="color: black;">&#40;</span>error<span style="color: black;">&#41;</span>:
    <span style="color: #ff7700;font-weight:bold;">pass</span>
&nbsp;
&nbsp;
<span style="color: #808080; font-style: italic;">#################################################################################</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> asm_to_mif<span style="color: black;">&#40;</span>text<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">''</span><span style="color: #483d8b;">'translate asm code to mif file
    return (data_content,code_content)
    '</span><span style="color: #483d8b;">''</span>
&nbsp;
    lexer  = lex.<span style="color: black;">lex</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>       <span style="color: #808080; font-style: italic;"># Return lexer object</span>
    <span style="color: #dc143c;">parser</span> = yacc.<span style="color: black;">yacc</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>     <span style="color: #808080; font-style: italic;"># Return parser object</span>
    <span style="color: #dc143c;">parser</span>.<span style="color: black;">symbol_table</span> = <span style="color: black;">&#123;</span><span style="color: black;">&#125;</span>
    <span style="color: #dc143c;">parser</span>.<span style="color: black;">parse</span><span style="color: black;">&#40;</span>text,lexer=lexer,debug=<span style="color: #ff4500;">1</span>,tracking=<span style="color: #008000;">True</span><span style="color: black;">&#41;</span>
&nbsp;
    symbol_table = <span style="color: #dc143c;">parser</span>.<span style="color: black;">symbol_table</span>
    data_file_content = <span style="color: #dc143c;">parser</span>.<span style="color: black;">data_file_content</span>
    code_file_content = <span style="color: #dc143c;">parser</span>.<span style="color: black;">code_file_content</span>
    <span style="color: #808080; font-style: italic;">#replace the symbols</span>
    <span style="color: #ff7700;font-weight:bold;">for</span> key <span style="color: #ff7700;font-weight:bold;">in</span> symbol_table.<span style="color: black;">keys</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>:
        code_file_content = code_file_content.<span style="color: black;">replace</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">"{%s_16}"</span> <span style="color: #66cc66;">%</span> key , dec2bin<span style="color: black;">&#40;</span>symbol_table<span style="color: black;">&#91;</span>key<span style="color: black;">&#93;</span>,<span style="color: #ff4500;">16</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>
        code_file_content = code_file_content.<span style="color: black;">replace</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">"{%s_24}"</span> <span style="color: #66cc66;">%</span> key , dec2bin<span style="color: black;">&#40;</span>symbol_table<span style="color: black;">&#91;</span>key<span style="color: black;">&#93;</span>,<span style="color: #ff4500;">24</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>
    <span style="color: #808080; font-style: italic;">#convert bin to hex</span>
    <span style="color: #ff7700;font-weight:bold;">def</span> bin_to_hex<span style="color: black;">&#40;</span>match<span style="color: black;">&#41;</span>:
        <span style="color: #ff7700;font-weight:bold;">return</span> <span style="color: #483d8b;">"%08x"</span> <span style="color: #66cc66;">%</span> <span style="color: #008000;">int</span><span style="color: black;">&#40;</span>match.<span style="color: black;">group</span><span style="color: black;">&#40;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#41;</span>,<span style="color: #ff4500;">2</span><span style="color: black;">&#41;</span>
    code_file_content = <span style="color: #dc143c;">re</span>.<span style="color: black;">sub</span><span style="color: black;">&#40;</span>r<span style="color: #483d8b;">'BIN_([0-1]{32})'</span>,bin_to_hex,code_file_content<span style="color: black;">&#41;</span><span style="color: #66cc66;">;</span>
    <span style="color: #808080; font-style: italic;">#add orignal line as commment</span>
    lines = text.<span style="color: black;">split</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'<span style="color: #000099; font-weight: bold;">\n</span>'</span><span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">def</span> get_orginal_line<span style="color: black;">&#40;</span>match<span style="color: black;">&#41;</span>:
        <span style="color: #ff7700;font-weight:bold;">return</span> lines<span style="color: black;">&#91;</span><span style="color: #008000;">int</span><span style="color: black;">&#40;</span>match.<span style="color: black;">group</span><span style="color: black;">&#40;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span><span style="color: black;">&#93;</span>
    code_file_content = <span style="color: #dc143c;">re</span>.<span style="color: black;">sub</span><span style="color: black;">&#40;</span>r<span style="color: #483d8b;">'{LINE_([0-9]+)}'</span>,get_orginal_line,code_file_content<span style="color: black;">&#41;</span><span style="color: #66cc66;">;</span>
    <span style="color: #ff7700;font-weight:bold;">return</span> <span style="color: black;">&#40;</span>data_file_content,code_file_content<span style="color: black;">&#41;</span>
&nbsp;
&nbsp;
<span style="color: #ff7700;font-weight:bold;">if</span> __name__ == <span style="color: #483d8b;">'__main__'</span>:
    <span style="color: #ff7700;font-weight:bold;">if</span> <span style="color: #008000;">len</span><span style="color: black;">&#40;</span><span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#41;</span> <span style="color: #66cc66;">&lt;</span> <span style="color: #ff4500;">2</span>:
        <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"please give a file name"</span>
    <span style="color: #ff7700;font-weight:bold;">else</span>:
        <span style="color: #ff7700;font-weight:bold;">try</span>:
            f = <span style="color: #008000;">open</span><span style="color: black;">&#40;</span><span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span>,<span style="color: #483d8b;">'r'</span><span style="color: black;">&#41;</span>
            text = f.<span style="color: black;">read</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
            f.<span style="color: black;">close</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
            <span style="color: black;">&#40;</span>d,c<span style="color: black;">&#41;</span> = asm_to_mif<span style="color: black;">&#40;</span>text<span style="color: black;">&#41;</span>
            df = <span style="color: #008000;">open</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">"dmem16.mif"</span>,<span style="color: #483d8b;">'w'</span><span style="color: black;">&#41;</span>
            df.<span style="color: black;">write</span><span style="color: black;">&#40;</span>d<span style="color: black;">&#41;</span>
            df.<span style="color: black;">close</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
            cf = <span style="color: #008000;">open</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">"prgmip32.mif"</span>,<span style="color: #483d8b;">'w'</span><span style="color: black;">&#41;</span>
            cf.<span style="color: black;">write</span><span style="color: black;">&#40;</span>c<span style="color: black;">&#41;</span>
            cf.<span style="color: black;">close</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>            
        <span style="color: #ff7700;font-weight:bold;">except</span> <span style="color: #008000;">IOError</span>,e:
            <span style="color: #ff7700;font-weight:bold;">print</span>  e</pre>
      </td>
    </tr>
  </table>
</div>