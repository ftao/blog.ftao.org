---
title: No Referer Extension For Firefox
author: Filia.Tao
layout: post
permalink: /2007/07/28/no-referer-extension-for-firefox/
categories:
  - Firefox
tags:
  - Firefox
  - noreferer@filia.cn
  - 防盗链
---
最近写epiphany 的扩展写的好累,文档太少. C的水平又太差..  
写个了Firefox的扩展练练手.  
起因是国内一些Blog 的图片有防盗链,不允许外联. (比如网易,新浪,百度空间等).  
在Google Reader 里看的很不爽.  
其实这些防盗链的基本原理就是根据引用页(Http Head 中的referer 项)  
扩展的原理就是对这些地址的图片发送空的引用页, 就如同你在一个新窗口打开图片一样.  
所以如果你在一个新窗口中可以正常浏览图片, 安装这个扩展以后你就能任何页面上浏览这张图片.

  * 下载: [noreferer@filia.cn][1]
  * 安装: 下载后,用firefox 打开. 就可以安装了.
  * 配置: 地址栏输入about:config, 输入noreferer.domains, 双击编辑. 输入不要发送引用页的域. 比如 我的设置是”163.com;sohu.com;sina.com;hi.baidu.com;hiphotos.baidu.com”,不同域之间用”;”分隔.  
    (如果不存在noreferer.domains这个项目,就建立一个.右键 新建->字符串 . 输入noreferer.domains )
  * 如果你想测试一下, 可以访问http://filia.tao.googlepages.com/test 应该可以看见一张图片(提示文件来自小百合), 你在noreferer.domains 中加入bbs.nju.edu.cn 后,Ctrl + F5刷新,应该就可以看见图片了.

&#8211;EOF&#8211;

 [1]: http://no-referer.googlecode.com/files/noreferer@filia.cn-0.1.xpi