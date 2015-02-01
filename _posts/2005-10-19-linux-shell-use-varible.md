---
title: '从编程语言的观点看linux shell&#8212;&#8211;变量的使用'
author: Filia.Tao
layout: post
permalink: /2005/10/19/linux-shell-use-varible/
categories:
  - Linux
---
<div>
  以BASH 为讨论的shell 语言<br /> 从编程语言的角度，变量的作用是非常重要的。<strong>1.变量的声明</strong><br /> 一般变量不需要声明，或使用 declare 变量名<br /> <strong>2.变量的赋值</strong><br /> 变量名=值 或 declare 变量名=值 （注意“=” 两边不能有空格）<br /> <strong>3.常量的问题</strong><br /> 一般没有常量的说法，不过有只读变量的。<br /> 用 readonly 变量名 或 declare -r 变量名 设置只读变量 有的书上讲只读变量 可以重新赋值，不能清空（或者说删除）。但我试验一下，似乎不能重新赋值。<br /> <strong>4.清空变量</strong><br /> unset 变量名 <strong /> </p> 
  
  <p>
    <strong>5.变量的值的引用</strong> $变量名 （即在变量名前加一个”$”符号就可以引用变量的值）必要时可在变量名为加花括号（如${num}）<br /> 一定要分清变量和变量的值之间的关系，只有在引用变量的值时才要加”$”，在给变量赋值，声明变量等时不能加”$”。<br /> <em>容易犯的错误</em><br /> 1.上面的赋值方法只适用于之间的值的赋值，不可用于将其它函数的返回，命令的输出等赋值给变量。<br /> 此种情况需要运用反引用，命令替换等方法。 2.普通变量的取名跟普通的编程语言相似（字母，数字，下划线），但是经常可以看见$1,$2,$*,$# 等符号，这些是位置变量和其它的一些特殊的变量，有特殊的用法和用途。</div> 
    
    <p>
      <img height="1" src="http://c.services.spaces.live.com/CollectionWebService/c.gif?space=why-gudu&#038;page=RSS%3a+%e4%bb%8e%e7%bc%96%e7%a8%8b%e8%af%ad%e8%a8%80%e7%9a%84%e8%a7%82%e7%82%b9%e7%9c%8blinux+shell-----%e5%8f%98%e9%87%8f%e7%9a%84%e4%bd%bf%e7%94%a8&#038;referrer=" width="1" border="0" /><img height="1" src="http://c.live.com/c.gif?NC=31263&#038;NA=1149&#038;PI=73329&#038;RF=&#038;DI=3919&#038;PS=85545&#038;TP=why-gudu.spaces.live.com&#038;GT1=why-gudu%3b2052" width="1" />
    </p>
