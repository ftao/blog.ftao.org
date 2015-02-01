---
title: 从编程语言的观点看linux shell-变量的作用域，环境变量，位置变量
author: Filia.Tao
layout: post
permalink: /2005/10/27/linux-shell-use-varible-scope-env-position-varible/
categories:
  - Linux
---
<div>
  我们仍以BASH 为讨论的shell 语言每一种编程语言中，我们都会碰到变量的作用域的问题。（比如在函数中定义的变量在函数外不能使用的)<br /> BASH 中也有类似的问题，局部变量和环境变量（全局变量）。<br /> 局部变量是普通的变量，仅在创建它的Shell中有效。<br /> 环境变量。我们更多的使用“环境变量”而不是“全局变量”，因为这个名称更<br /> 能体现它的特点。环境变量对创建它的Shell及其派生出来的子进程都有效。或者说环境变量可以继承，但它对其它与创建它的Shell没有关系的Shell并不可见。 </p> 
  
  <p>
    声明环境变量：declare -x 变量名＝值
  </p>
  
  <p>
    export 变量名＝值
  </p>
  
  <p>
    export<br /> 已经定义的局部变量名<br /> 在谈谈另一类的重要变量，位置变量。函数，脚本等都需要参数，位置变量就是
  </p>
  
  <p>
    用来获得这些参数的。相当于其它编程语言的形参。<br /> $0 表示当前脚本文件名。<br /> $1,$2,$3,&#8230;&#8230;..,$9,${10},&#8230;. 表示第1，2，3&#8230;&#8230;.个参数。
  </p>
  
  <p>
    $# 位置变量的个数<br /> $* $@ 表示所有的位置变量。（当然，两者还是有区别的。）
  </p>
</div>

<img height="1" src="http://c.services.spaces.live.com/CollectionWebService/c.gif?space=why-gudu&#038;page=RSS%3a+%e4%bb%8e%e7%bc%96%e7%a8%8b%e8%af%ad%e8%a8%80%e7%9a%84%e8%a7%82%e7%82%b9%e7%9c%8blinux+shell-%e5%8f%98%e9%87%8f%e7%9a%84%e4%bd%9c%e7%94%a8%e5%9f%9f%ef%bc%8c%e7%8e%af%e5%a2%83%e5%8f%98%e9%87%8f%ef%bc%8c%e4%bd%8d%e7%bd%ae%e5%8f%98%e9%87%8f&#038;referrer=" width="1" border="0" /><img height="1" src="http://c.live.com/c.gif?NC=31263&#038;NA=1149&#038;PI=73329&#038;RF=&#038;DI=3919&#038;PS=85545&#038;TP=why-gudu.spaces.live.com&#038;GT1=why-gudu%3b2052" width="1" />