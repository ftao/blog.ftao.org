---
title: how to write lex (11)
author: Filia.Tao
layout: post
permalink: /2007/05/07/how-to-write-lex-11/
categories:
  - compiler
  - lex
tags:
  - compiler
  - lex
---
DFA最小化的详细算法  
<span style="font-style: italic; background-color: #ffffff; color: #cc0000">说明:下面的算法在空间复杂度上有一点小问题,我已经改用deque来实现.算法的基本思想是一致的.<br /> 下面的算法比使用deque的实现方式更通俗易懂,所以没有更新下面的描述了.</span>  
数据结构：

  * 一个MAP map<int,int> mapping　表示原来的状态编号对应到最小化以后的状态编号  
    mapping[1] = 2 表示原来的状态１，对应到最小化后状态２中。
  * 一个min-DFA 状态列表sss。其中的元素为未最小化的DFA的状态的集合。  
    上面的MAP中的最小化状态就用列表的下标表示。
  * 一个循环控制量count

算法：

  1. 把DFA的状态分成若干个集合。  
    一个是非终态集合。终态集合中对应不同规则的终态放到不同集合中。  
    把这些集合加入到sss中。count = sss.size();
  2. 初始化mapping
  3. 如果count > 0 从sss中取下一个集合,否则到6
  4. 遍历字母表判断是否可以分割这个集合。(关于是否可以分割的定义,书本上都有，这里就不写了)  
    如果某一个字符可以分割这个集合.</p> 
      * 将分割后的来两个集合s1,s2加入到sss中，
      * 对连个集合中的状态更新mapping.  
        对s1中的状态i，mapping[i] = sss.size() -2 ;  
        对s2中的状态j, mapping[j] = sss.size()-1;
      * count = sss.size() &#8211; 当前集合的在sss中下标 -1
      * 退出字母表遍历
    
    如果任何字符都不能分割这个集合
    
      * 将当前集合加入到sss的末尾。
      * 对集合中的状态更新mapping.  
        对当前集合中的状态i，mapping[i] = sss.size() -1 ;
      * count &#8211;
  5. 回到3
  6. 从mapping 中收集信息,建立min-DFA.(用mdfa 表示，原DFA用dfa表示） 
      * 初态　mdfa.initialState = mapping[dfa.initialState]
      * 状态　foreach( i in dfa.states) mdfa.states.insert(mapping[i])
      * 转换  
        foreach( t in dfa.transitions)  
        mdfa.transitions.insert(triple(mapping[t->from],mapping[t->to],mapping[t->label]))
      * 终态和meta data  
        foreach( s in dfa.finalStates)</p> <p style="margin-left: 40px">
          mdfa.finalStates.insert(mapping[dfa.finalStates])<br /> if (!mdfa.meta[mapping[s]] || mdfa.meta[mapping[s]] > dfa.meta[s])<br /> //这里体现了lex输入文件中先出现的规则优先的原则。mdfa.meta[mapping[s]] = dfa.mata[s]</li> </ul> </li> </ol>
