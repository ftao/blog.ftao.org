---
title: 数据结构总结－堆
author: Filia.Tao
layout: post
permalink: /2007/10/14/data-struct-heap/
categories:
  - 算法
  - 计算机科学
tags:
  - heap
  - 堆
  - 数据结构
---
堆结构是一类很重要的数据结构，特别是在实现优先队列方面。  
堆结构实际上都是树（或森林），不过按节点的存储方式又可以分为两种

  1. 顺序存储  
    这种情况下，必须保持是完全树。 （在插入,删除后及时调整)  
    可以通过下标运算找到父节点，字节点。
  2. 链接存储  
    如果是二叉树，一般就用LeftChild,RightChild 两个指针。  
    否则一般用环链表表示一个 节点的子女。

下面的表格在blog 中显得很拥挤，建议在<a href="http://docs.google.com/Doc?id=d3nxdfg_72hjf6pg" title="数据结构－堆－@doc.google.com" target="_blank">这里</a>查看.

<address>
  <em><br /> </em>
</address>

<table id="yz70" border="1" bordercolor="#333333" cellpadding="3" cellspacing="0" height="793" width="489">
  <tr>
    <td width="70">
      数据结构
    </td>
    
    <td width="70">
      存储方式
    </td>
    
    <td width="150">
      定义与性质
    </td>
    
    <td width="100">
      支持的操作 及 复杂度（分摊）
    </td>
  </tr>
  
  <tr>
    <td width="70">
      最大堆
    </td>
    
    <td width="70">
      顺序存储
    </td>
    
    <td width="150">
      完全二叉树，最大树
    </td>
    
    <td width="100">
      插入<br /> O(log n)<br /> 删除最大元素 O(log n)
    </td>
  </tr>
  
  <tr>
    <td width="70">
      最小最大堆
    </td>
    
    <td width="70">
      顺序存储
    </td>
    
    <td width="150">
      完全二叉树，树的各层交替为最小层和最大层
    </td>
    
    <td width="100">
      插入<br /> O(log n)<br /> 删除最大元素 O(log n)<br /> 删除最小元素 O(log n)
    </td>
  </tr>
  
  <tr>
    <td width="70">
      双堆
    </td>
    
    <td width="70">
      顺序存储
    </td>
    
    <td width="150">
      完全二叉树<br /> 或者为空,或者同时满足:<br /> 1.根结点不含元素<br /> 2.左子树是最小堆<br /> 3.右子树为最大堆<br /> 4.若右子树不为空,设i 为左子树中的任意结点,<br /> j为右子树的对应结点.<br /> 若j不存在,则令j为右子树中对应i的父结点的结点。<br /> 必有结点i的key 小于 结点j的key值
    </td>
    
    <td width="100">
      插入<br /> O(log n)<br /> 删除最大元素 O(log n)<br /> 删除最小元素 O(log n)
    </td>
  </tr>
  
  <tr>
    <td width="70">
      左偏树<br /> (最小/最大)
    </td>
    
    <td width="70">
      链接存储
    </td>
    
    <td width="150">
      1.是左偏树<br /> 每一个内部结点x 有<br /> shortest(LeftChild(x))<br /> >=shortes(RightChild(x))<br /> 2.是最小/最大树
    </td>
    
    <td width="100">
      插入<br /> O(log n)<br /> 删除最小/最大元素 O(log n)<br /> 合并 O(log n)
    </td>
  </tr>
  
  <tr>
    <td width="70">
      二项式堆<br /> (最小/最大)
    </td>
    
    <td width="70">
      链接存储
    </td>
    
    <td width="150">
      最小/最大二项式树的集合.<br /> 度为k的二项式树(记作B<sub>k</sub>)定义为:<br /> 若k=0,该树只有1个结点.<br /> 若k>0,该树的根的度为k,其子树为B<sub></sub>,B<sub>1</sub>,&#8230;..,B<sub>k-1</sub>
    </td>
    
    <td width="100">
      插入 O(1)<br /> 删除最小/最大元素 O(log n)<br /> 合并 O(1)
    </td>
  </tr>
  
  <tr>
    <td width="70">
      斐波纳契堆<br /> (最小/最大)
    </td>
    
    <td width="70">
      链接存储
    </td>
    
    <td width="150">
      最小/最大树的集合.<br /> 二项式堆是斐波纳契堆的特殊情况.<br /> 使用双环链表,并在结点中加入parent和childcut.<br /> 关键是瀑布修剪
    </td>
    
    <td width="100">
      插入 O(1)<br /> 删除最小/最大元素 O(log n)<br /> 合并 O(1)<br /> 减少指定结点key值<br /> O(1)<br /> 删除指定结点元素<br /> O(log n)
    </td>
  </tr>
</table>