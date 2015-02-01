---
title: OOC (L2) 语言解决 S先生与P先生谜题
author: Filia.Tao
layout: post
permalink: /2008/04/08/solove-mrs-and-mrp-problem-with-oo/
categories:
  - python
  - 随便写写
tags:
  - ooc
  - python
  - 毕业设计
---
前几天看到这篇Blog, [Python解S先生与P先生谜题][1]  
正好写毕业设计的解释器的需要测试程序, 觉得实现一个这个问题的解法比实现快速排序之类的有趣多了。

(题目描述是从上面文章中拷贝过来的. )  
题目：S先生与P先生谜题

设有两个自然数X、Y，2<=X<=Y<=99,S先生知道这两个数的和S，P先生知道这两个数的积P，  
他们二人进行了如下对话：

* S：我确信你不知道这两个数是什么，但我也不知道。  
* P: 一听你说这句话，我就知道这两个数是什么了。  
* S: 我也是，现在我也知道了。

现在你能通过他们的会话推断出这两个数是什么吗？（当然，S和P先生都是非常聪明的）

我用OOC 语言实现 S先生与P先生谜题, 大体框架使用上面的文章中的内容  
(关于OOC 语言的定义请查看[L2Define][2]  
解释器由Python实现,可以从 <http://code.google.com/p/finterpretor/> 获得.  
)  
代码如下:

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
</pre>
      </td>
      
      <td class="code">
        <pre class="ooc" style="font-family:monospace;">class Main {
&nbsp;
    const MIN = 2, MAX = 99;
&nbsp;
    //只需要判断y的范围, 程序确保x的取值范围
    static
    func int BaseCondition(int x, int y)
    {
        y &gt;= MIN && y &lt;= MAX && y &gt;= x
    }
&nbsp;
    //S先生的S分析
    func int[][] S(int x, int y)
    {
        var int[][] ar; int s,i,len end
        s = x + y;
        len = s/2 + 1 - MIN;
        ar = new int[][len];
        i = 0;
        while(i &lt; len)
        (
            ar[i] = new int[2];
            ar[i][0] = i + MIN;
            ar[i][1] = s - i - MIN;
            i++
        );
        ar
    }
&nbsp;
    //P先生的P分析
    func int[][] P(int x, int y)
    {
        var int[][] ar; int p,i,len end
        p = x * y;
        i = MIN;
        len = 0;
        while( i &lt;= p/i)
        (
            if (p % i == 0 &&  BaseCondition(i, p/i))
                len ++;
            i ++
        );
        ar = new int[][len];
        i = MIN;
        len = 0;
        while( i &lt;= p/i)
        (
            if (p % i == 0 &&  BaseCondition(i, p/i))
            (
                ar[len] = new int[2];
                ar[len][0] = i;
                ar[len][1] = p/i;
                len ++
            );
            i++
        );
        ar
    }
&nbsp;
    //条件1 - S：我确信你不知道这两个数是什么，但我也不知道ﾷￖￎ￶
    func int Condition1(int x, int y)
    {
        var int[][] s_sep; int i,ret end
        s_sep = S(x, y);
&nbsp;
        ret = 1;
        if (s_sep.length == 1)
        (
            ret = 0
        )
        else
        (
            i = 0;
            while (ret && i &lt; s_sep.length)
            (
                if (P(s_sep[i][0], s_sep[i][1]).length &lt;= 1)
                    ret = 0;
&nbsp;
                i++
            )
        );
        ret
    }
&nbsp;
    //条件2 - P: 一听你说这句话，我就知道这两个数是什么了。ﾷￖￎ￶
    func int Condition2(int x, int y)
    {
        var int[][] p_sep; int i,ret end
        p_sep = P(x,y);
        i = 0 ;
        ret = 1;
        while (ret && i &lt; p_sep.length )
        (
            if (x == p_sep[i][0] && y == p_sep[i][1])
            (
                 ret
            )
            else
            (
                if (Condition1(p_sep[i][0], p_sep[i][1]))
                (
&nbsp;
                    ret = 0
                 )
            );
            i++
        );
        ret
    }
&nbsp;
    //条件3 - S: 我也是，现在我也知道了。ﾷￖￎ￶
    func int Condition3(int x, int y)
    {
        var int[][] s_sep; int i,ret end
        s_sep = S(x,y);
        i = 0 ;
        ret = 1;
        while (ret && i &lt; s_sep.length)
        (
            if (x == s_sep[i][0] && y == s_sep[i][1])
            (
                ret
            )
            else
            (
                if (Condition2(s_sep[i][0], s_sep[i][1]))
                    ret = 0
            );
            i++
        );
        ret
    }
&nbsp;
    //需要同时满足上面3个条件
    func int Condition(int x, int y)
    {
        Condition1(x, y ) &&  Condition2(x, y) && Condition3(x, y)
    }
&nbsp;
    func void main()
    {
        var int i,j,x,y end
        i = MIN;
        x = 0;
        y = 0;
        while ( i &lt;= MAX && x == 0 && y == 0)
        (
            j = i;
            while(j &lt; MAX && x == 0 && y == 0)
            (
                print (i);
                println (j);
                if(Condition(i,j))
                (
                    x = i;
                    y = j
                );
                j++
            );
            i ++
        );
        print (x);
        print (y)
    }
&nbsp;
}</pre>
      </td>
    </tr>
  </table>
</div>

代码高亮由wp-syntax (基于 geshi) 提供,随便写了个语法文件.

 [1]: http://hyry.dip.jp/blogt.py?file=0200.blog
 [2]: http://code.google.com/p/finterpretor/wiki/L2Define
