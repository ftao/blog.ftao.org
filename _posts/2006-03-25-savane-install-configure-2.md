---
title: 安装 配置Savane(二)
author: Filia.Tao
layout: post
permalink: /2006/03/25/savane-install-configure-2/
categories:
  - Linux
---
根据上面的给出的提示，我们还要运行四个命令。make ,make database ,make conf,make install.

make 命令，很简单，一般没什么错误。

make database 是建立初始的数据库，如果你刚才提供了数据库的附加选项，可能会要求你输入数据库密码。

make conf 是很重要的一个过程。配置选项很多，没必要一一列举。我指出几个可能会发生错误的。

  * 1) Default hostname 如果有域名，填就可以了。否则填局域网地址或localhost.
  * 2) HTTPS hostname 可以暂时不填
  * 4) SQL database hostname 填localhost
  * 5) SQL database name 填./configure 时提供的数据库名
  * 6-7 数据库用户名和密码随便填，不过请马上打开mysql,运行类似 GRANT ALL ON savane.* TO user_name@localhost IDENTIFY
  * 13) Server administration project unix name 填一个合法的本机unix 用户名，我的做法是新建一个savane 用户。
  * 14) Default locale 似乎不支持中文，写en-EN
  * 16) Default theme 17) Logo name 主题和Logo图片。
  * 18) Mail domain : 这个有点麻烦，填你的域名。实在不行就填localhost。但是似乎有的邮件服务器会不接受它找不到发送地址的邮件。（我试过gmail能收到，126 ,yahoo 似乎都不认。）
  * 19) Admin mail address 如果可以，填一个合法的邮件用户名，没有也能用。
  * 其它的注释掉或用它提供的example 就可以了。

make install 也很简单，就是拷贝一些文件到相应的目录而已。