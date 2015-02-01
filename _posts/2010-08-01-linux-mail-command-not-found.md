---
title: linux 没有mail 命令怎么办？
author: Filia.Tao
layout: post
permalink: /2010/08/01/linux-mail-command-not-found/
categories:
  - Linux
---
debian 系统的cron job 的输出是已mail 方式发送给用户的。 之前一直是用mail 命令看邮件的。 上次不知道改了什么，mail 命令不见了。  
`<br />
-bash: mail: command not found<br />
`  
google 了一下，装一下mailx 就可以了。 之前真懒， google 1 分钟就能解决的问题，都没有去解决。  
`<br />
sudo apt-get install mailx<br />
`