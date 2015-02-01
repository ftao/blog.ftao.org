---
title: 复习C++
author: Filia.Tao
layout: post
permalink: /2007/10/05/review-cpp/
categories:
  - C/C++
tags:
  - C++
---
很久不写C++代码了, 最近复习数据结构, 算法.顺便重新写点代码, 把C++的基础找回来, 也实践一下Effective C++中的一些条款.  
写了一个二叉搜索树的模板类. 把C++的一些基本知识都用上了, 比如类,模板,重载,引用,指针等等.

一些思考

  * 模板, 除了STL , 实际的程序中有模板到底使用情况如何?
  * 命名风格
  * 注释. C++ 有比较流行的文档注释方式吗?
  * Effective STL 条款21: 尽可能使用const . 这一条我是体会深刻啊.
  * 使用Graphviz 进行可视化还是不错的. 不过有时候还是不够灵活.

下面是代码, 其实里面有很多可以/需要说的地方&#8230;.  
<!--more-->

<div class="wp_syntax">
  <div class="code">
    <pre class="cpp" style="font-family:monospace;"><span style="color: #0000ff;">template</span> <span style="color: #000080;">&lt;</span><span style="color: #0000ff;">class</span> KT<span style="color: #000080;">&gt;</span>
<span style="color: #0000ff;">class</span> Element
<span style="color: #008000;">&#123;</span>
<span style="color: #0000ff;">public</span><span style="color: #008080;">:</span>
	<span style="color: #0000ff;">typedef</span> KT key_type<span style="color: #008080;">;</span>
	Element<span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#123;</span><span style="color: #008000;">&#125;</span>
	Element<span style="color: #008000;">&#40;</span>KT key<span style="color: #008000;">&#41;</span><span style="color: #008000;">&#123;</span>this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>_key <span style="color: #000080;">=</span> key<span style="color: #008080;">;</span><span style="color: #008000;">&#125;</span>
	Element<span style="color: #008000;">&#40;</span><span style="color: #0000ff;">const</span> Element<span style="color: #000080;">&lt;</span>KT<span style="color: #000080;">&gt;</span><span style="color: #000040;">&</span> arg<span style="color: #008000;">&#41;</span><span style="color: #008000;">&#123;</span>this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>_key <span style="color: #000080;">=</span> arg._key<span style="color: #008080;">;</span><span style="color: #008000;">&#125;</span>
	key_type key<span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #0000ff;">const</span> 
	<span style="color: #008000;">&#123;</span>
		<span style="color: #0000ff;">return</span> this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>_key<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
<span style="color: #0000ff;">private</span><span style="color: #008080;">:</span>
	KT _key<span style="color: #008080;">;</span>
	<span style="color: #666666;">//maybe something else</span>
<span style="color: #008000;">&#125;</span><span style="color: #008080;">;</span>
&nbsp;
<span style="color: #ff0000; font-style: italic;">/**
BSTNode 二叉搜索树节点的定义 
DT 应该提供一个操作key(), 返回DT::key_type 类型的一个值 
DT::key_type 类型应该提供 &lt; , == , &lt;= 等比较操作符的实现 
*/</span> 
<span style="color: #0000ff;">template</span> <span style="color: #000080;">&lt;</span><span style="color: #0000ff;">class</span> DT<span style="color: #000080;">&gt;</span>
<span style="color: #0000ff;">class</span> BSTNode
<span style="color: #008000;">&#123;</span>
<span style="color: #0000ff;">public</span><span style="color: #008080;">:</span>
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		uid <span style="color: #000080;">=</span> unique_id<span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
		left <span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008080;">;</span>
		right <span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008080;">;</span>
&nbsp;
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">int</span> uid<span style="color: #008080;">;</span>
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #000040;">*</span> left<span style="color: #008080;">;</span>
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #000040;">*</span> right<span style="color: #008080;">;</span>
	DT data<span style="color: #008080;">;</span>
	<span style="color: #0000ff;">void</span> output_as_dot<span style="color: #008000;">&#40;</span>ostream<span style="color: #000040;">&</span> os<span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
<span style="color: #008000;">&#125;</span><span style="color: #008080;">;</span>
&nbsp;
<span style="color: #0000ff;">template</span> <span style="color: #000080;">&lt;</span><span style="color: #0000ff;">class</span> DT<span style="color: #000080;">&gt;</span>
<span style="color: #0000ff;">void</span> BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #008080;">::</span><span style="color: #007788;">output_as_dot</span><span style="color: #008000;">&#40;</span>ostream<span style="color: #000040;">&</span> os<span style="color: #008000;">&#41;</span>
<span style="color: #008000;">&#123;</span>
	os<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">"<span style="color: #000099; font-weight: bold;">\t</span>node"</span><span style="color: #000080;">&lt;&lt;</span>this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>uid<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">"[label="</span><span style="color: #FF0000;">"&lt;&lt;this-&gt;data.key()&lt;&lt;"</span><span style="color: #FF0000;">"];"</span><span style="color: #000080;">&lt;&lt;</span>endl<span style="color: #008080;">;</span>
	<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left <span style="color: #000040;">!</span><span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		os<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">""</span>node<span style="color: #FF0000;">"&lt;&lt;this-&gt;uid&lt;&lt;"</span><span style="color: #FF0000;">" -&gt; "</span>node<span style="color: #FF0000;">"&lt;&lt;this-&gt;left-&gt;uid&lt;&lt;"</span><span style="color: #FF0000;">";"</span><span style="color: #000080;">&lt;&lt;</span>endl<span style="color: #008080;">;</span>
		this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>output_as_dot<span style="color: #008000;">&#40;</span>os<span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">else</span>
	<span style="color: #008000;">&#123;</span>
		<span style="color: #0000ff;">int</span> tmp <span style="color: #000080;">=</span> unique_id<span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
		os<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">"<span style="color: #000099; font-weight: bold;">\t</span>node"</span><span style="color: #000080;">&lt;&lt;</span>tmp<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">"[label="</span>NIL<span style="color: #FF0000;">",shape=box];"</span><span style="color: #000080;">&lt;&lt;</span>endl<span style="color: #008080;">;</span>
		os<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">""</span>node<span style="color: #FF0000;">"&lt;&lt;this-&gt;uid&lt;&lt;"</span><span style="color: #FF0000;">" -&gt; "</span>node<span style="color: #FF0000;">"&lt;&lt;tmp&lt;&lt;"</span><span style="color: #FF0000;">";"</span><span style="color: #000080;">&lt;&lt;</span>endl<span style="color: #008080;">;</span>       
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right <span style="color: #000040;">!</span><span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		os<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">""</span>node<span style="color: #FF0000;">"&lt;&lt;this-&gt;uid&lt;&lt;"</span><span style="color: #FF0000;">" -&gt; "</span>node<span style="color: #FF0000;">"&lt;&lt;this-&gt;right-&gt;uid&lt;&lt;"</span><span style="color: #FF0000;">";"</span><span style="color: #000080;">&lt;&lt;</span>endl<span style="color: #008080;">;</span>
		this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>output_as_dot<span style="color: #008000;">&#40;</span>os<span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">else</span>
	<span style="color: #008000;">&#123;</span>
		<span style="color: #0000ff;">int</span> tmp <span style="color: #000080;">=</span> unique_id<span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
		os<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">"<span style="color: #000099; font-weight: bold;">\t</span>node"</span><span style="color: #000080;">&lt;&lt;</span>tmp<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">"[label="</span>NIL<span style="color: #FF0000;">",shape=box];"</span><span style="color: #000080;">&lt;&lt;</span>endl<span style="color: #008080;">;</span>
		os<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">""</span>node<span style="color: #FF0000;">"&lt;&lt;this-&gt;uid&lt;&lt;"</span><span style="color: #FF0000;">" -&gt; "</span>node<span style="color: #FF0000;">"&lt;&lt;tmp&lt;&lt;"</span><span style="color: #FF0000;">";"</span><span style="color: #000080;">&lt;&lt;</span>endl<span style="color: #008080;">;</span>          
	<span style="color: #008000;">&#125;</span>
<span style="color: #008000;">&#125;</span>
&nbsp;
&nbsp;
<span style="color: #0000ff;">template</span> <span style="color: #000080;">&lt;</span><span style="color: #0000ff;">class</span> DT<span style="color: #000080;">&gt;</span>
<span style="color: #0000ff;">class</span> BST
<span style="color: #008000;">&#123;</span>
<span style="color: #0000ff;">public</span><span style="color: #008080;">:</span>
	BST<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>root <span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	DT<span style="color: #000040;">*</span> search<span style="color: #008000;">&#40;</span><span style="color: #0000ff;">typename</span> DT<span style="color: #008080;">::</span><span style="color: #007788;">key_type</span> key<span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
	<span style="color: #0000ff;">void</span> output_as_dot<span style="color: #008000;">&#40;</span>ostream<span style="color: #000040;">&</span> os<span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
&nbsp;
	<span style="color: #0000ff;">bool</span> insert<span style="color: #008000;">&#40;</span><span style="color: #0000ff;">const</span> DT<span style="color: #000040;">&</span> toadd<span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
&nbsp;
	DT<span style="color: #000040;">*</span> del<span style="color: #008000;">&#40;</span><span style="color: #0000ff;">typename</span> DT<span style="color: #008080;">::</span><span style="color: #007788;">key_type</span> key<span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
<span style="color: #0000ff;">private</span><span style="color: #008080;">:</span>
	<span style="color: #0000ff;">void</span> del<span style="color: #008000;">&#40;</span>BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span> <span style="color: #000040;">*</span> parent,BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span> <span style="color: #000040;">*</span> todel<span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
&nbsp;
<span style="color: #0000ff;">private</span><span style="color: #008080;">:</span>
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span> <span style="color: #000040;">*</span> root<span style="color: #008080;">;</span>
<span style="color: #008000;">&#125;</span><span style="color: #008080;">;</span>
&nbsp;
<span style="color: #0000ff;">template</span> <span style="color: #000080;">&lt;</span><span style="color: #0000ff;">class</span> DT<span style="color: #000080;">&gt;</span>
DT<span style="color: #000040;">*</span> BST<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #008080;">::</span><span style="color: #007788;">search</span><span style="color: #008000;">&#40;</span><span style="color: #0000ff;">typename</span> DT<span style="color: #008080;">::</span><span style="color: #007788;">key_type</span> key<span style="color: #008000;">&#41;</span>
<span style="color: #008000;">&#123;</span>
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #000040;">*</span> node <span style="color: #000080;">=</span> this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>root<span style="color: #008080;">;</span>
	<span style="color: #0000ff;">while</span><span style="color: #008000;">&#40;</span>node <span style="color: #000040;">!</span><span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>key <span style="color: #000080;">==</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>data.<span style="color: #007788;">key</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span>
			<span style="color: #0000ff;">return</span> <span style="color: #000040;">&</span>node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>data<span style="color: #008080;">;</span>
		<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>key <span style="color: #000080;">&lt;</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>data.<span style="color: #007788;">key</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span>
			node <span style="color: #000080;">=</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left<span style="color: #008080;">;</span>
		<span style="color: #0000ff;">else</span>
			node <span style="color: #000080;">=</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">return</span> <span style="color: #0000ff;">NULL</span><span style="color: #008080;">;</span>
<span style="color: #008000;">&#125;</span>
&nbsp;
<span style="color: #0000ff;">template</span> <span style="color: #000080;">&lt;</span><span style="color: #0000ff;">class</span> DT<span style="color: #000080;">&gt;</span>
<span style="color: #0000ff;">bool</span> BST<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #008080;">::</span><span style="color: #007788;">insert</span><span style="color: #008000;">&#40;</span><span style="color: #0000ff;">const</span> DT<span style="color: #000040;">&</span> toadd<span style="color: #008000;">&#41;</span>
<span style="color: #008000;">&#123;</span>
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #000040;">*</span> node <span style="color: #000080;">=</span> this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>root<span style="color: #008080;">;</span>
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #000040;">*</span> p <span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008080;">;</span>
	<span style="color: #0000ff;">while</span><span style="color: #008000;">&#40;</span>node <span style="color: #000040;">!</span><span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		p <span style="color: #000080;">=</span> node<span style="color: #008080;">;</span>
		<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>toadd.<span style="color: #007788;">key</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span> <span style="color: #000080;">==</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>data.<span style="color: #007788;">key</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span>
		<span style="color: #008000;">&#123;</span>
			<span style="color: #0000ff;">return</span> <span style="color: #0000ff;">false</span><span style="color: #008080;">;</span>
		<span style="color: #008000;">&#125;</span>
		<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>toadd.<span style="color: #007788;">key</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span> <span style="color: #000080;">&lt;</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>data.<span style="color: #007788;">key</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span>
			node <span style="color: #000080;">=</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left<span style="color: #008080;">;</span>
		<span style="color: #0000ff;">else</span>
			node <span style="color: #000080;">=</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
&nbsp;
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span> <span style="color: #000040;">*</span> new_node <span style="color: #000080;">=</span> <span style="color: #0000dd;">new</span> BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
	new_node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left <span style="color: #000080;">=</span> new_node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right <span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008080;">;</span>
	new_node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>data <span style="color: #000080;">=</span> toadd<span style="color: #008080;">;</span>
&nbsp;
	<span style="color: #0000ff;">if</span> <span style="color: #008000;">&#40;</span>this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>root <span style="color: #000080;">==</span> <span style="color: #0000ff;">NULL</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>root <span style="color: #000080;">=</span> new_node<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">else</span>
	<span style="color: #008000;">&#123;</span>
		<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>toadd.<span style="color: #007788;">key</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span> <span style="color: #000080;">&lt;</span> p<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>data.<span style="color: #007788;">key</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span>
			p<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left <span style="color: #000080;">=</span> new_node<span style="color: #008080;">;</span>
		<span style="color: #0000ff;">else</span>
			p<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right <span style="color: #000080;">=</span> new_node<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">return</span> <span style="color: #0000ff;">true</span><span style="color: #008080;">;</span>
&nbsp;
<span style="color: #008000;">&#125;</span>
&nbsp;
<span style="color: #0000ff;">template</span> <span style="color: #000080;">&lt;</span><span style="color: #0000ff;">class</span> DT<span style="color: #000080;">&gt;</span>
<span style="color: #0000ff;">void</span> BST<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #008080;">::</span><span style="color: #007788;">del</span><span style="color: #008000;">&#40;</span>BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span> <span style="color: #000040;">*</span> parent, BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span> <span style="color: #000040;">*</span> todel<span style="color: #008000;">&#41;</span>
<span style="color: #008000;">&#123;</span>
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span> <span style="color: #000040;">*</span>child<span style="color: #008080;">;</span>
	<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>todel<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left <span style="color: #000080;">==</span> <span style="color: #0000ff;">NULL</span> <span style="color: #000040;">||</span> todel<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right <span style="color: #000080;">==</span> <span style="color: #0000ff;">NULL</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		child <span style="color: #000080;">=</span> todel<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left<span style="color: #000080;">==</span><span style="color: #0000ff;">NULL</span> <span style="color: #008080;">?</span> todel<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right <span style="color: #008080;">:</span> todel<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">else</span>
	<span style="color: #008000;">&#123;</span>
		BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span> <span style="color: #000040;">*</span> pc <span style="color: #000080;">=</span> parent<span style="color: #008080;">;</span>
		child <span style="color: #000080;">=</span> todel<span style="color: #008080;">;</span>
		<span style="color: #0000ff;">while</span><span style="color: #008000;">&#40;</span>child<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left <span style="color: #000040;">!</span><span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008000;">&#41;</span> 
		<span style="color: #008000;">&#123;</span>
			pc <span style="color: #000080;">=</span> child<span style="color: #008080;">;</span>
			child <span style="color: #000080;">=</span> child<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left<span style="color: #008080;">;</span>
		<span style="color: #008000;">&#125;</span>
		pc<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left <span style="color: #000080;">=</span> child<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right<span style="color: #008080;">;</span>    <span style="color: #666666;">//child为左子数中最小值 </span>
		child<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left <span style="color: #000080;">=</span> todel<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left<span style="color: #008080;">;</span>
		child<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right <span style="color: #000080;">=</span> todel<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>parent <span style="color: #000080;">==</span> <span style="color: #0000ff;">NULL</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>root <span style="color: #000080;">=</span> child<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">else</span>
	<span style="color: #008000;">&#123;</span>
		<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>parent<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left <span style="color: #000080;">==</span> todel<span style="color: #008000;">&#41;</span>
			parent<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left <span style="color: #000080;">=</span> child<span style="color: #008080;">;</span>
		<span style="color: #0000ff;">else</span>
			parent<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right <span style="color: #000080;">=</span> child<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
&nbsp;
&nbsp;
<span style="color: #008000;">&#125;</span>
&nbsp;
<span style="color: #0000ff;">template</span> <span style="color: #000080;">&lt;</span><span style="color: #0000ff;">class</span> DT<span style="color: #000080;">&gt;</span>
DT<span style="color: #000040;">*</span> BST<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #008080;">::</span><span style="color: #007788;">del</span><span style="color: #008000;">&#40;</span><span style="color: #0000ff;">typename</span> DT<span style="color: #008080;">::</span><span style="color: #007788;">key_type</span> key<span style="color: #008000;">&#41;</span>
<span style="color: #008000;">&#123;</span>
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #000040;">*</span> node <span style="color: #000080;">=</span> this<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>root<span style="color: #008080;">;</span>
	BSTNode<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #000040;">*</span> p <span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span><span style="color: #008080;">;</span>
	<span style="color: #0000ff;">while</span><span style="color: #008000;">&#40;</span>node <span style="color: #000040;">!</span><span style="color: #000080;">=</span> <span style="color: #0000ff;">NULL</span> <span style="color: #000040;">&&</span> key <span style="color: #000040;">!</span><span style="color: #000080;">=</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>data.<span style="color: #007788;">key</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		p <span style="color: #000080;">=</span> node<span style="color: #008080;">;</span>
		<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>key <span style="color: #000080;">&lt;</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>data.<span style="color: #007788;">key</span><span style="color: #008000;">&#40;</span><span style="color: #008000;">&#41;</span><span style="color: #008000;">&#41;</span>
			node <span style="color: #000080;">=</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>left<span style="color: #008080;">;</span>
		<span style="color: #0000ff;">else</span>
			node <span style="color: #000080;">=</span> node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>right<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">if</span><span style="color: #008000;">&#40;</span>node <span style="color: #000080;">==</span> <span style="color: #0000ff;">NULL</span><span style="color: #008000;">&#41;</span>
	<span style="color: #008000;">&#123;</span>
		<span style="color: #0000ff;">return</span> <span style="color: #0000ff;">NULL</span><span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
	<span style="color: #0000ff;">else</span>
	<span style="color: #008000;">&#123;</span>
		del<span style="color: #008000;">&#40;</span>p,node<span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
		<span style="color: #0000ff;">return</span> <span style="color: #000040;">&</span>node<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>data<span style="color: #008080;">;</span>
	<span style="color: #008000;">&#125;</span>
<span style="color: #008000;">&#125;</span>
&nbsp;
<span style="color: #0000ff;">template</span> <span style="color: #000080;">&lt;</span><span style="color: #0000ff;">class</span> DT<span style="color: #000080;">&gt;</span>
<span style="color: #0000ff;">void</span> BST<span style="color: #000080;">&lt;</span>DT<span style="color: #000080;">&gt;</span><span style="color: #008080;">::</span><span style="color: #007788;">output_as_dot</span><span style="color: #008000;">&#40;</span>ostream<span style="color: #000040;">&</span> os<span style="color: #008000;">&#41;</span>
<span style="color: #008000;">&#123;</span>
	os<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">"digraph G {"</span><span style="color: #000080;">&lt;&lt;</span>endl<span style="color: #008080;">;</span>
	os<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">"splines=line;"</span><span style="color: #000080;">&lt;&lt;</span>endl<span style="color: #008080;">;</span>
	root<span style="color: #000040;">-</span><span style="color: #000080;">&gt;</span>output_as_dot<span style="color: #008000;">&#40;</span>os<span style="color: #008000;">&#41;</span><span style="color: #008080;">;</span>
	os<span style="color: #000080;">&lt;&lt;</span><span style="color: #FF0000;">"}"</span><span style="color: #000080;">&lt;&lt;</span>endl<span style="color: #008080;">;</span>
<span style="color: #008000;">&#125;</span></pre>
  </div>
</div>