---
title: 安装 配置Savane(一)
author: Filia.Tao
layout: post
permalink: /2006/03/25/saven-install-configure/
categories:
  - Linux
---
最近在机器上装Savane（<font size="-1"><font color="#cc0033">Savane</font>&#8216; is a Web-based Free Software hosting system, originally based on SourceForge 2.0.）</font>它的网站[在这][1]。现在基本能用了。把过程写一下，免得自己都忘了。

第一步，下载。到<font face="#mce_temp_font#">http://download.gna.org/savane/下载最新版本。我下载的是savane-1.4的源代码。（savane-1.4.tar.gz）在http://download.gna.org/savane-doc/ </font><font face="#mce_temp_font#">下载文档</font>

第二步, savane 本身的安装，看下面的命令

<div>
  <pre>[root@MagicLinux src]# cd /usr/src/savane-1.4   [root@MagicLinux savane-1.4]# ./configure      checking for which... yes (GNU)      checking for make... yes      checking for bash... yes      checking for perl... yes      checking for sed... yes      checking for find... yes      checking for gettext... yes      checking for mysql... yes      checking for mailman... no      checking for automake... yes      checking for autoconf... yes      checking for makeinfo... yes      checking for gzip... yes      checking for GNU/Linux distribution... unable to guess

Do you want the configuration file creation process to be interactive?      ex: yes      ex: no      [yes]: yes

I could not identify your distribution.      Therefore, could you please provide your      default web server username?      ex: www      ex: apache      [apache]apache

What is the URL directory of Savane on your http server?      ex: for sv.gnu.org/ the answer is /      ex: for bla.org/savane/ the answer is /savane      [/]: /savane

Where should we store configuration files?      Choose one of the following.      ex: /etc/savannah      (WARNING: if you use something else than the default setting,       be sure to set SAVANE_CONF environment variable in both Apache and terminals you use,</pre>
  
  <pre>it is required for the frontend and the backend)      [/etc/savannah]:

Where should we install binaries and scripts?      ex: /usr/bin      ex: /usr/local/bin      [/usr/local/bin]:

Where should we install perl modules?      ex: /usr/local/lib/site_perl      [/usr/local/lib/site_perl]:

Where should we install i18n files?      ex: /usr/share/locale      ex: /usr/local/share/locale      [/usr/share/locale]:

What is the database name?      ex: savane      [savane]: savane

Any options to use for ?      ex: you may add for mysql, if you do not have a ~/.my.cnf      something like -u user -p      (never include password in clear text here)      options:

creating ./backend/Makefile...      creating ./db/Makefile...      creating ./doc/devel/Makefile...      creating ./doc/Makefile...      creating ./etc/Makefile...      creating ./frontend/php/images/Makefile...      creating ./frontend/php/css/Makefile...      creating ./lib/Makefile...      creating ./Makefile...      creating ./po/Makefile...

Now, you should continue by doing the following:              ./configure              # build what need to be built              make              # log as superuser              su              # create the database              make database              # create the configuration file              make conf              # install the backend scripts and the translation files              make install

# OR do all of the above all at once              ./configure              su -c "make savane"

# Afterwards, you should check              # http://127.0.0.1/pathtosavane/testconfig.php

If you use the backend, read also etc/README.

(please read carefully INSTALL.verbose for a more complete guide)

# build what need to be built              make              # log as superuser              su              # create the database              make database              # create the configuration file              make conf              # install the backend scripts and the translation files              make install

# OR do all of the above all at once

su -c "make savane"

# Afterwards, you should check              # http://127.0.0.1/pathtosavane/testconfig.php

If you use the backend, read also etc/README.

(please read carefully INSTALL.verbose for a more complete guide)</pre>
</div>

<pre>这里的大多数选项默认就可以了， 提示也很到位。有可能有问题的是Web Server UserName, 可以查看apache 的httpd.conf的user项。 database name可以随便选，因为你还没有建立嘛. 数据库的附加选项，你如果有root密码有-u root -p 就可以了。（我是偷懒，没设密码.)</pre>

<pre>未完待续。</pre>

 [1]: https://gna.org/projects/savane/https://gna.org/projects/savane/
