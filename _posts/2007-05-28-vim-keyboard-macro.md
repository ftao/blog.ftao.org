---
title: vim 的键盘宏
author: Filia.Tao
layout: post
permalink: /2007/05/28/vim-keyboard-macro/
categories:
  - Linux
tags:
  - Linux
  - marco
  - vim
---
目的：在所有php文件的开头加一句<?php ob_start();?>

实现：使用vim的键盘宏。

> vim *.php  
> qa  
> <ESC>  
> gg  
> ^  
> i  
> <?php ob_start();?>  
> <ESC>  
> :wnext  
> q  
> 100@a

我录制了一个一个键盘宏来实现在文件开头加入一行<?php ob_start();?> 然后将这个宏重复执行足够多的次数。 100 不是一个精确的数字，只是目录下的php文件数目小于100.
