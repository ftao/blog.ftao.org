---
title: fInterpretor中静态语义检查的实现
author: Filia.Tao
layout: post
permalink: /2008/04/27/static-semantic-checking-in-finterpretor/
categories:
  - compiler
  - python
tags:
  - ast
  - compiler
  - fInterpretor
  - python
  - type
  - 类型检查
---
这几天在解释器fInterpretor 中加入了静态语义检查

<div id="wikicontent">
  <p>
    静态语义检查分为下面几个方面:
  </p>
  
  <ol>
    <li>
      名字检查 <ul>
        <li>
          类型名
        </li>
        <li>
          函数名
        </li>
        <li>
          变量名
        </li>
      </ul>
    </li>
    
    <li>
      类型匹配检查 <ul>
        <li>
          各种运算 (比如算术,逻辑)的类型约束
        </li>
        <li>
          函数调用实参和形参的类型匹配
        </li>
        <li>
          函数实际返回类型和声明的返回类型是否匹配
        </li>
      </ul>
    </li>
    
    <li>
      变量初始化检查 (非必须) <ul>
        <li>
          检查局部变量在引用前是否已经初始化 (尚未实现)
        </li>
      </ul>
    </li>
  </ol>
</div>

<div id="wikicontent">
  现在基本对L1 语言做好了上述的检查, 中间做了好多弯路.
</div>

<div>
  主要是在进行静态类型检查的时候在类型约束信息放在哪里的这个问题上一开始做了错误的选择,过度依赖Python的语言特性修饰器,动态的添加类型约束到一个统一的地方.
</div>

<div>
  最终的选择还是比较”分布式的”, 每个类型定义一个 do_type_trans(self, op_name, arg = None) 方法对各种操作进行类型约束检查,同时返回对应操作后正确的类型(类型计算), 这样还有一个好处就是将”类型计算”的逻辑移动到类型定义文件中,而不是写在ASTAction 中.
</div>

<div>
</div>

<div>
  嗯.基本就是这样了. 争取在5.1前把这些加入到L2语言中. 还有更重要的就是测试了. 特别是需要覆盖测试,测试目标语言的各种特性是否被正确实现了.
</div>

<div>
</div>

<div>
</div>
