---
date: 2013-03-25
categories:
  - linux
  - 个人
  - 技术
tags:
  - remmina
title: remmina的bug
---

今天链接一个重装了系统的windows的时候出现了问题,用户名密码都没变但是remmina一直提示无法链接到远程主机....

在不知道怎么查了数据之后发现了问题所在,详细bug讨论参见官方:[https://bugs.launchpad.net/ubuntu/+source/remmina/+bug/944040](https://bugs.launchpad.net/ubuntu/+source/remmina/+bug/944040)

反正最终的解决方案就是:删<code>'~/.freerdp/known_hosts</code>,再链接就好了- =囧