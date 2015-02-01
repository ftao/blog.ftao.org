---
title: 安装 配置Savane(三)
author: Filia.Tao
layout: post
permalink: /2006/04/01/savane-install-configure/
categories:
  - Linux
---
要使Savane正确的工作，我们还要好好的配置系统、Apache 、PHP和exim

apache的配置：  
设置虚拟主机或Alias,使URL能正确的指向savane-1.4/frontend/php  
比如： Alias /savane /usr/src/savane-1.4/frontend/php

php的配置：

编译php时必须加入gettext 和mail函数.  
加入gettext 必须在./configure 时加入 &#8211;with-gettext 选项。（前提是系统中含有gettext工具)  
mail支持不需要加入特殊的选项，但是必须让./configure 能找到sendmail。（比如一般sendmail 在/usr/sbin/sendmail,注意如果你是重新编译PHP，请一定执行make clean先，否则还是不会加入mail函数)  
当然mysql的支持也要加入。  
我的configure 命令如下: ./configure &#8211;prefix=/usr/local/php4  
&#8211;with-gettext &#8211;with-mysql=/usr/local/mysql &#8211;with-apxs2=/usr/local/apache2/bin/apxs

mail服务器，一般按文档的要求安装exim（因为sendmail太复杂了).假设我们把exim安装在/usr/exim ,我们在/usr/sbin 下执行ln -s /usr/exim/bin/exim sendmail，这个操作请在编译PHP前执行。exim的设置在 /usr/exim/configure ,内容很明了。如有问题，请查看exim的文档。
