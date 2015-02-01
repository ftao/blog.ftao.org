---
title: 使用 reStructuredText 编写用户手册
author: Filia.Tao
layout: post
permalink: /2011/05/28/write-user-manual-using-restructredtest-and-sphinx/
categories:
  - 编程开发
tags:
  - github
  - python
  - reStructuredText
---
前几天，将ezEngage 的帮助文档重新写了一遍。这么做的原因是原来的文档直接用HTML写的，并且和主站放在一起。  
发布不方便，只是修改一点东西也要和主站一起发布。并且原始文件就是HTML，写起来很麻烦。

我用reStructuredText + Sphinx + github pages 重新编写，部署和发布了一份帮助文档。

reStructuredText 是一种轻量级的标记语言，特点是原始文件可读性很好。  
Python 以及很多Python著名的开源项目（比如Django) 都使用该格式的来编写文档和手册。　  
Sphinx 的一个将reStructuredText 文件转换成各种格式（比如HTML,LeTex,PDF 等)的工具。　  
GitHub Pages 是一个GitHub 提供的托管静态页面的服务，只要内容通过git 推送到github 上面，就很可以方便发布内容了。　

步骤如下:  
1. 在github 上面建立一个仓库，开通github pages 功能  
2. 将master 和 gh-pages 两个分支checkout 到本地的两个目录 (比如说 example-master , example-gh-pages)  
3. 在master branch 里面正常写 reStructuredText ,  
4. 符号链接 example-master/_build/html 　到 example-gh-pages  
5. make html , 生成html  
6. push gh-pages 分支到 github , 发布完成。

这个方案的好处:  
1. 源文件可读性好  
2. 有版本管理  
3. github pages 发布很方便  
4. 服务器都省了&#8230;. 

链接:  
1. 这份文档对应的仓库: <https://github.com/ezengage/ezengage-site/>  
2. GitHub Pages <http://pages.github.com/>  
3. Sphinx <http://sphinx.pocoo.org>

ps: reStructuredText 和 RESTful 这两个拼写上都和 rest 相近， Sphinx （文档生成工具, http://sphinx.pocoo.org) 以及 Sphinx (全文搜索引擎, http://sphinxsearch.com/) 都叫Sphinx 。搜索的时候很容易混淆。　