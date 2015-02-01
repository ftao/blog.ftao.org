---
title: 'yacc,ast  and graphviz'
author: Filia.Tao
layout: post
permalink: /2007/06/15/yaccast-and-graphviz/
categories:
  - compiler
tags:
  - ast
  - compiler
  - graphviz
  - yacc
---
这几天忙着写编译原理课程设计的另一部分. Seu YACC的实现.

这个程序的核心算法实现不是有我来完成,我负责后端输出的模板代码的书写.

实际上就是一个基于LALR分析表的parser.

我附带加上了生成抽象语法树的功能, 并输出DOT 文件,通过graphviz 生成图片.

下面贴一段Min-C 语言的抽象语法树.(图片比较大)

[<img src="http://blog.filia.cn/wp-content/uploads/2007/06/ast.png" title="ast.png" dragover="true" alt="ast.png" height="130" width="411" />][1]

对应的代码为  
`<br />
int main ( )<br />
{<br />
int x = 0 ;<br />
int b = 2 ;<br />
int c = 5 ;<br />
int d = 6 ;<br />
int a = b ;<br />
if ( x < y )<br />
{<br />
int x = y ;<br />
}<br />
while ( c == d )<br />
{<br />
int m = c + d ;<br />
m = x + y ;<br />
}<br />
}<br />
`

 [1]: http://blog.filia.cn/wp-content/uploads/2007/06/ast.png "ast.png"