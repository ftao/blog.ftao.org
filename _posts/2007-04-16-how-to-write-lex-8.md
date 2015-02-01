---
title: how to write lex (8)
author: Filia.Tao
layout: post
permalink: /2007/04/16/how-to-write-lex-8/
categories:
  - compiler
  - lex
tags:
  - compiler
  - lex
  - re
---
lex输入文件解析.  
格式:

> The general format of *lex* source shall be:
> 
> > <pre><em>Definitions</em>

<strong>%%</strong>

<em>Rules</em>

<strong>%%</strong>

<em>User</em>Subroutines</pre>

Definions:这个有两部分

  * lex 定义文件中使用的变量  
    在%{ 和%}之间的内部的是C声明, 简单的将它们拷贝下来,发送最终输出文件的开头.  
    另外的是lex的变量定义(以及其他一些东西,我们忽略)  
    每一行是name  substitute  对.空格分割.name必须是一个合法的C变量名.  
    在这里和规则定义部分将{name}替换成substitute.  
    也就是说这里**可能有嵌套**的定义.
  * 规则定义部分  
    这部分以%% 开始,以 %% 结束.  
    分两列RE       ACTION  
    两个之间的分割符为空格(或其他空字符比如\t)  
    注意ACTION 可能出现多行的情况,一般情况下会还有{和}把ACTION包围起来.(不过lex规范没有定义这一点)  
    对于RE中出现的空白字符,必须符合下面的条件</p> 
      * 整个表达式在双引号中
      * 空白字符出现在双引号或[]中
      * 每个空白字符有一个前导反斜杠
    
    正则表达式中可能出现{name}需要用第一部分定义的substitute  替换.  
    然后我们把用两个数组分别记录RE和ACTION 就可以了.</li> 
    
      * 用户自定义的函数  
        简单的将拷贝到输出文件就可以了.</ul>