---
title: 用xapian和xappy做全文搜索
author: Filia.Tao
layout: post
permalink: /2009/05/02/indexer-using-xapian-xappy/
categories:
  - python
  - 编程开发
tags:
  - pychseg
  - python
  - xapian
  - xappy
  - 搜索
---
今天尝试使用xapian 和xappy 以及 pychseg 做了一下数据库的全文索引, 发现比我想象的简单.

 首先介绍一下背景知识:

  * <a href="http://xapian.org/" target="_blank">xapian </a>是一个开源的搜索引擎. C++语言实现,并且有Perl,Python,PHP 等语言的接口.  (类似java 中的lucene)
  * <a href="http://code.google.com/p/xappy/" target="_blank">xappy </a>是对xapian 的Python 接口的进一步的封装.  
  * <a href="http://code.google.com/p/pychseg/" target="_blank">pychseg</a> 是基于python的中文分词项目。 

 首先下载和安装xapian.  按照 <http://xapian.org/download>  这个页面的说明, 安装 xapian-core 和xapian-bindings(实际上只要xapian-python 就可以了). 

然后是安装xappy , 这个很简单  easy_install xappy 就可以了.  

最后是pychseg .

<pre>svn checkout http://pychseg.googlecode.com/svn/trunk/ pychseg-read-only
cd pychseg-read-only
python setup.py install</pre>

不过这个代码有点问题, 一个有一处中间有路径使用window 的风格, 还有一处有函数没有定义. (应用一下这个[patch][1]就可以了.)

安装好以后复制<a href="http://code.google.com/p/xappy/source/browse/trunk/examples/" target="_blank">xappy/examples </a>下面的的文件一份. 然后改一下.  
下面是做索引的代码. 搜索的代码基本用原来的例子就可以了. 比我想象的简单.

<div class="wp_syntax">
  <div class="code">
    <pre class="python" style="font-family:monospace;"><span style="color: #808080; font-style: italic;">#!/usr/bin/env python</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">sys</span>
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">os</span>
<span style="color: #ff7700;font-weight:bold;">import</span> xappy
<span style="color: #ff7700;font-weight:bold;">from</span>  pychseg.<span style="color: black;">mmseg</span>.<span style="color: black;">algorithms</span> <span style="color: #ff7700;font-weight:bold;">import</span> SimpleAlgorithm 
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> tokens<span style="color: black;">&#40;</span>text<span style="color: black;">&#41;</span>:
    a = SimpleAlgorithm<span style="color: black;">&#40;</span>text<span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">return</span> <span style="color: black;">&#91;</span><span style="color: #008000;">str</span><span style="color: black;">&#40;</span>w<span style="color: black;">&#41;</span> <span style="color: #ff7700;font-weight:bold;">for</span> w <span style="color: #ff7700;font-weight:bold;">in</span> a.<span style="color: black;">segment</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span><span style="color: black;">&#93;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> create_index<span style="color: black;">&#40;</span>dbpath<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">"""Create a new index, and set up its field structure.
    """</span>
    iconn = xappy.<span style="color: black;">IndexerConnection</span><span style="color: black;">&#40;</span>dbpath<span style="color: black;">&#41;</span>
    iconn.<span style="color: black;">add_field_action</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'book_id'</span>, xappy.<span style="color: black;">FieldActions</span>.<span style="color: black;">STORE_CONTENT</span><span style="color: black;">&#41;</span>
    iconn.<span style="color: black;">add_field_action</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'book_id'</span>, xappy.<span style="color: black;">FieldActions</span>.<span style="color: black;">INDEX_EXACT</span><span style="color: black;">&#41;</span>
    iconn.<span style="color: black;">add_field_action</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'book_name'</span>, xappy.<span style="color: black;">FieldActions</span>.<span style="color: black;">STORE_CONTENT</span><span style="color: black;">&#41;</span>
    iconn.<span style="color: black;">add_field_action</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'book_name'</span>, xappy.<span style="color: black;">FieldActions</span>.<span style="color: black;">INDEX_EXACT</span><span style="color: black;">&#41;</span>
    iconn.<span style="color: black;">add_field_action</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'author_id'</span>, xappy.<span style="color: black;">FieldActions</span>.<span style="color: black;">STORE_CONTENT</span><span style="color: black;">&#41;</span>
    iconn.<span style="color: black;">add_field_action</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'author_id'</span>, xappy.<span style="color: black;">FieldActions</span>.<span style="color: black;">INDEX_EXACT</span><span style="color: black;">&#41;</span>
    iconn.<span style="color: black;">add_field_action</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'author_name'</span>, xappy.<span style="color: black;">FieldActions</span>.<span style="color: black;">STORE_CONTENT</span><span style="color: black;">&#41;</span>
    iconn.<span style="color: black;">add_field_action</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'author_name'</span>, xappy.<span style="color: black;">FieldActions</span>.<span style="color: black;">INDEX_EXACT</span><span style="color: black;">&#41;</span>
    iconn.<span style="color: black;">add_field_action</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'text'</span>, xappy.<span style="color: black;">FieldActions</span>.<span style="color: black;">INDEX_FREETEXT</span><span style="color: black;">&#41;</span>
    iconn.<span style="color: black;">close</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> open_index<span style="color: black;">&#40;</span>dbpath<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">"""Open an existing index.
    """</span>
    <span style="color: #ff7700;font-weight:bold;">return</span> xappy.<span style="color: black;">IndexerConnection</span><span style="color: black;">&#40;</span>dbpath<span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> cn_seg<span style="color: black;">&#40;</span>text<span style="color: black;">&#41;</span>:
    s =  <span style="color: #483d8b;">" "</span>.<span style="color: black;">join</span><span style="color: black;">&#40;</span>tokens<span style="color: black;">&#40;</span>text<span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">return</span> s
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> index_book<span style="color: black;">&#40;</span>iconn, book<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">"""Index a book."""</span>
    doc = xappy.<span style="color: black;">UnprocessedDocument</span><span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>
    doc.<span style="color: black;">fields</span>.<span style="color: black;">append</span><span style="color: black;">&#40;</span>xappy.<span style="color: black;">Field</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'book_id'</span>, <span style="color: #008000;">str</span><span style="color: black;">&#40;</span>book<span style="color: black;">&#91;</span><span style="color: #483d8b;">'book_id'</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>
    doc.<span style="color: black;">fields</span>.<span style="color: black;">append</span><span style="color: black;">&#40;</span>xappy.<span style="color: black;">Field</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'book_name'</span>, book<span style="color: black;">&#91;</span><span style="color: #483d8b;">'book_name'</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>
    doc.<span style="color: black;">fields</span>.<span style="color: black;">append</span><span style="color: black;">&#40;</span>xappy.<span style="color: black;">Field</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'author_id'</span>, <span style="color: #008000;">str</span><span style="color: black;">&#40;</span>book<span style="color: black;">&#91;</span><span style="color: #483d8b;">'author_id'</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>
    doc.<span style="color: black;">fields</span>.<span style="color: black;">append</span><span style="color: black;">&#40;</span>xappy.<span style="color: black;">Field</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'author_name'</span>, book<span style="color: black;">&#91;</span><span style="color: #483d8b;">'author_name'</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>
    doc.<span style="color: black;">fields</span>.<span style="color: black;">append</span><span style="color: black;">&#40;</span>xappy.<span style="color: black;">Field</span><span style="color: black;">&#40;</span><span style="color: #483d8b;">'text'</span>, cn_seg<span style="color: black;">&#40;</span>book<span style="color: black;">&#91;</span><span style="color: #483d8b;">'book_name'</span><span style="color: black;">&#93;</span> + <span style="color: #483d8b;">'<span style="color: #000099; font-weight: bold;">\n</span>'</span> +  book<span style="color: black;">&#91;</span><span style="color: #483d8b;">'author_name'</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span><span style="color: black;">&#41;</span>
    doc.<span style="color: #008000;">id</span> = <span style="color: #483d8b;">"book_%d"</span> <span style="color: #66cc66;">%</span>book<span style="color: black;">&#91;</span><span style="color: #483d8b;">'book_id'</span><span style="color: black;">&#93;</span>
    iconn.<span style="color: black;">add</span><span style="color: black;">&#40;</span>doc<span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> index_all<span style="color: black;">&#40;</span>iconn<span style="color: black;">&#41;</span>:
    <span style="color: #483d8b;">"""Index all book."""</span>
    count = <span style="color: #ff4500;"></span>
    i = <span style="color: #ff4500;"></span>
    num_per_page = <span style="color: #ff4500;">1000</span>
    <span style="color: #ff7700;font-weight:bold;">while</span> <span style="color: #008000;">True</span>:
        <span style="color: #808080; font-style: italic;">#下面一句是伪代码</span>
        books = <span style="color: #808080; font-style: italic;"># book_db.fetchall().limit(num_per_page , i * num_per_page)</span>
        <span style="color: #ff7700;font-weight:bold;">for</span> book <span style="color: #ff7700;font-weight:bold;">in</span> books:
            index_book<span style="color: black;">&#40;</span>iconn, book<span style="color: black;">&#41;</span>
        count += <span style="color: #008000;">len</span><span style="color: black;">&#40;</span>books<span style="color: black;">&#41;</span>
        i += <span style="color: #ff4500;">1</span>
        <span style="color: #ff7700;font-weight:bold;">if</span> <span style="color: #008000;">len</span><span style="color: black;">&#40;</span>books<span style="color: black;">&#41;</span> <span style="color: #66cc66;">&lt;</span> num_per_page:
            <span style="color: #ff7700;font-weight:bold;">break</span>
    <span style="color: #ff7700;font-weight:bold;">return</span> count
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> main<span style="color: black;">&#40;</span>argv<span style="color: black;">&#41;</span>:
    dbpath = <span style="color: #483d8b;">'dbindex'</span>
    create_index<span style="color: black;">&#40;</span>dbpath<span style="color: black;">&#41;</span>
    iconn = open_index<span style="color: black;">&#40;</span>dbpath<span style="color: black;">&#41;</span>
    count = index_all<span style="color: black;">&#40;</span>iconn<span style="color: black;">&#41;</span>
    <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">"Indexed %d book."</span> <span style="color: #66cc66;">%</span> count
&nbsp;
<span style="color: #ff7700;font-weight:bold;">if</span> __name__ == <span style="color: #483d8b;">'__main__'</span>:
    main<span style="color: black;">&#40;</span><span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#41;</span></pre>
  </div>
</div>

 这个做出来的索引基本还是可以用的,但是中文有时候的分词还是不太好.

<a name="pychseg_patch"></a>  
附件:pychseg_path

<div class="wp_syntax">
  <div class="code">
    <pre class="diff" style="font-family:monospace;">Index: pychseg/utils/myitertools.py
===================================================================
<span style="color: #888822;">--- pychseg/utils/myitertools.py        （修订版 33）</span>
<span style="color: #888822;">+++ pychseg/utils/myitertools.py        （工作拷贝）</span>
<span style="color: #440088;">@@ -23,6 +23,8 @@</span>
 #SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
 #
&nbsp;
<span style="color: #00b000;">+def val2cmp<span style="">&#40;</span>x<span style="">&#41;</span>:return x</span>
<span style="color: #00b000;">+</span>
 def takehighest<span style="">&#40;</span>iterable, key, reverse=True<span style="">&#41;</span>:
     """
     &gt;&gt;&gt; takehighest<span style="">&#40;</span>lambda x:x/<span style="">2</span>, <span style="">&#91;</span><span style="">9</span>,<span style="">3</span>,<span style="">4</span>,<span style="">1</span>,<span style="">4</span>,<span style="">9</span>,<span style="">2</span>,<span style="">8</span><span style="">&#93;</span><span style="">&#41;</span>
Index: pychseg/mmseg/worddict.py
===================================================================
<span style="color: #888822;">--- pychseg/mmseg/worddict.py   （修订版 33）</span>
<span style="color: #888822;">+++ pychseg/mmseg/worddict.py   （工作拷贝）</span>
<span style="color: #440088;">@@ -36,8 +36,8 @@</span>
 def load_dict<span style="">&#40;</span><span style="">&#41;</span>:
     word_dict = <span style="">&#123;</span><span style="">&#125;</span>
     load_path = pychseg.__path__<span style="">&#91;</span><span style=""></span><span style="">&#93;</span>
<span style="color: #991111;">-    chars = os.path.normpath<span style="">&#40;</span> os.path.join<span style="">&#40;</span>load_path, ".\\wordlist\\chars.lex" <span style="">&#41;</span> <span style="">&#41;</span></span>
<span style="color: #991111;">-    words = os.path.normpath<span style="">&#40;</span> os.path.join<span style="">&#40;</span>load_path, ".\\wordlist\\words.lex" <span style="">&#41;</span> <span style="">&#41;</span></span>
<span style="color: #00b000;">+    chars = os.path.normpath<span style="">&#40;</span> os.path.join<span style="">&#40;</span>load_path, "./wordlist/chars.lex" <span style="">&#41;</span> <span style="">&#41;</span></span>
<span style="color: #00b000;">+    words = os.path.normpath<span style="">&#40;</span> os.path.join<span style="">&#40;</span>load_path, "./wordlist/words.lex" <span style="">&#41;</span> <span style="">&#41;</span></span>
     logging.info<span style="">&#40;</span>"loading single chars dict"<span style="">&#41;</span>
     load_words<span style="">&#40;</span>chars, word_dict, "UTF-<span style="">8</span>"<span style="">&#41;</span>
     logging.info<span style="">&#40;</span>"loading words dict"<span style="">&#41;</span>
<span style="color: #440088;">@@ -64,4 +64,4 @@</span>
 if __name__ == "__main__":
     d = load_dict<span style="">&#40;</span><span style="">&#41;</span>
     print len<span style="">&#40;</span>d<span style="">&#41;</span>
<span style="color: #991111;">-</span>
\ 在文件
 末尾没有新行+
Index: pychseg/mmseg/config.py
===================================================================
<span style="color: #888822;">--- pychseg/mmseg/config.py     （修订版 33）</span>
<span style="color: #888822;">+++ pychseg/mmseg/config.py     （工作拷贝）</span>
<span style="color: #440088;">@@ -2,4 +2,4 @@</span>
&nbsp;
 WORD_MAX_LENGTH = <span style="">4</span>
&nbsp;
<span style="color: #991111;">-OUTPUT_CHARSET = 'gbk'</span>
\ 在文件
 末尾没有新行+OUTPUT_CHARSET = 'utf-<span style="">8</span>'</pre>
  </div>
</div>

(发现这个wp-syntax/genshi 还支持diff 格式, 很赞.)

 [1]: #pychseg_patch
