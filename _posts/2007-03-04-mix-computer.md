---
title: Mix计算机基本知识
author: Filia.Tao
layout: post
permalink: /2007/03/04/mix-computer/
categories:
  - 计算机科学
tags:
  - Computer
  - Mix
  - 指令
  - 汇编
  - 计算机科学
---
1.mix 字  
一个字节至少表达64个不同的值  
一个计算机字是5个字加一位符号位  
2.MIX的寄存器组织  
![][1]  
3.字的部分场  
场(L:R)在计算机中以8L+R的值的存入一个字节  
4.指令格式  
0 1 2 3 4 5  
± A A I F C  
C 操作码  
F 对操作码的说明，通常是一个场说明(L:R)=8L+R  
±AA 为地址  
I 变址说明 0 不变，1－6 采用I1-I6寄存器中的值与±AA相加，其结果作为指令的地址  
5.记法  
操作符 地址，I(F)  
6.指令的具体说明  
M为变址后的地址，CONTENTS(M)为对应地址的存储单元的内容

C F 说明  
LDA 8 场 CONTETNS(M)所说明的场代替寄存器A的内容  
LDX 15 场 CONTETNS(M)所说明的场代替寄存器X的内容  
LDi 8+i 场 CONTETNS(M)所说明的场代替寄存器X的内容  
如果指令导致置1,2,3字节为非0,则改指令未定义  
LDAN 16 场 跟上面的指令含义相同，不过把相反的符号装入  
LDXN 23 场  
LDiN 16+i 场  
STA 24 场  
STX 31 场 rX的内容代替CONTENTS(M)所说明的场。除非符号是场的一部分，否则符号不变  
STi 24+i 场 rIi的内容代替CONTENTS(M)所说明的场。除非符号是场的一部分，否则符号不变  
STJ 32 场 rJ的内容代替CONTENTS(M)所说明的场。除非符号是场的一部分，否则符号不变  
STZ 33 场 CONTENTS(M)所说明的场变为0。如果符号是场的一部分，置＋。

用V表示CONTENTS(M)所说明的场

ADD 1 场 V加到rA  
如溢出，溢出开关接通。如0，符号不变（下同）  
SUB 2 场 rA减去V，  
MUL 3 场 V乘rA的10字节的乘积放在A和X中。rA,rX的符号变为结果的符号。  
DIV 4 场 rA,rX的值看成10字节的数，符号看rA.除以V。商放rA.余数放rX，rX符号看rA原来的符号。

ENTA/ENNA 48 2/3 数量M/-M装入rA  
ENTX 55 2/3 数量M/-M装入rX  
ENTi/ENNi 48+i 2/3 数量M/-M装入rIi  
INCA/DECA 48 0/1 数量M加到rA/rA减去数量M  
INCX/DECX 55 0/1 数量M加到rX/rX减去数量M  
INCi/DECi 48+i 0/1 数量M加到rIi/rIi减去数量M

CMPA 56 场 A指定的场与CONTENTS(M)同样的场比较  
CMPX/CMPi 63/56+i 场 含义类似

JMP 39 0 无条件转移，下一条指令从单元M中取出。  
JSP 39 1 同上，但rJ内容不变  
JOV 39 2  
JNOV 39 3  
还有很多转移操作不再列出

MOVE 7 个数 从单元M开始，按F所指定的个数的那些字，移动到rI1所指定的单元起的一片单元，结束后rI1加上F的值  
SLA/SRA 6 0/1 M说明左移，右移的字节数。  
A左移/A右移  
SLAX/SRAX 6 2/3 AX左移/AX右移  
SLC/SRC 6 4/5 AX循环左移/AX循环右移  
NOP 0 空操作  
HLT 5 2 停机

[tag]Mix[/tag],[tag]Computer[/tag],[tag]汇编[/tag],[tag]指令[/tag]

 [1]: http://blog.filia.cn/wp-content/uploads/2007/03/mix-reg.jpg