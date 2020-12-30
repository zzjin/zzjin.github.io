---
date: 2011-09-30
categories:
  - linux
  - web
  - 技术
tags:
  - lnmp
  - php
  - 记录
title: lnmpa下面重新编译apache之后出现的问题
---

今天想重新编译php支持其他的属性.但是后来发现除了点问题.就打算直接重新编译apache里面的全套了...

在编译php的时候出现了点问题...mysql找不到了...囧.网上找了半天终于发现问题..php.ini的配置为问题啊~

本来lnmp的php-rpm的ini文件配置好好的mysql连接什么的..但是装了apache之后重新编译了php一遍支持httpd的接口.所以php.ini呗覆盖掉了...囧啊...强制修改mysql的sock之后问题基本解决了.但是之后还会不会出现什么问题只能静观其变了= =~

附上大牛的指导原帖地址:

<a title="测试&amp;解决方法" href="http://www.cenxiw.com/lnmp0-6-phpmyadmin%E6%97%A0%E6%B3%95%E7%99%BB%E9%99%86%E4%BB%A5%E5%8F%8Aphp%E6%97%A0%E6%B3%95%E8%BF%9E%E6%8E%A5mysql%E7%9A%84%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/" target="_blank">测试&amp;解决方法地址</a>