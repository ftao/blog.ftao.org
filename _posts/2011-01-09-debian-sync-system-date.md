---
title: debian 同步系统时间
author: Filia.Tao
layout: post
permalink: /2011/01/09/debian-sync-system-date/
categories:
  - Linux
tags:
  - debian
  - npdate
  - system time
---
刚才发现服务器的时间不对了。 更新了一下  
`<br />
sudo apt-get install ntpdate<br />
sudo ntpdate north-america.pool.ntp.org<br />
sudo hwclock --systohc<br />
`
