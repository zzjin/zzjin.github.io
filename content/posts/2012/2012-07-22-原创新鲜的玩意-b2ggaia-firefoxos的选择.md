---
date: 2012-07-22
categories:
  - web
  - 个人
  - 技术
tags:
  - B2G
  - firefox
  - FireOs
  - GAIA
  - html5
title: '[原创]新鲜的玩意--B2G+GAIA FirefoxOS的选择'
---

周末看到了新闻说fireOS的nightly build版本出来了~迫不及待的去下载了开始折腾....

虽然看介绍非常简单...但是其实还着着实实的折腾了好一阵子啊= =(两天啊....)

<!--more-->

先来点图爽爽现在的半成品界面:

![clock](http://bcytest.qiniudn.com/b2g/b2g_clock.png)
![clock_new](http://bcytest.qiniudn.com/b2g/b2g_clock_new.png)
![lockscreen](http://bcytest.qiniudn.com/b2g/b2g_lockscreen.png)
![mainframe](http://bcytest.qiniudn.com/b2g/b2g_mainframe.png)
![mess_new](http://bcytest.qiniudn.com/b2g/b2g_mess_new.png)
![mess_show](http://bcytest.qiniudn.com/b2g/b2g_mess_show.png)
![picture](http://bcytest.qiniudn.com/b2g/b2g_picture.png)
![setting_bright](http://bcytest.qiniudn.com/b2g/b2g_setting_bright.png)

说明:

主要需要三个文件,B2G Gaia xulrunner-sdk

按照官方wiki的说明的话,直接在linux下面make -C gaia profile的话会自动下载xul的sdk的.

编译好了profile之后放在win下面直接能运行.但是在中文系统下面打开的时候会由于时间字符串太长而挡住解锁按键:就像下面这样:

![home_error](http://bcytest.qiniudn.com/others/b2g_home_error.png)

非常的囧,不得不去看看他的profile的编译和运行方式.

其实他的系统管理几乎和android差不多的.一个xml文件配置配合一个zip文件就是不同的系统数据...

打开profile下面的system.XX的文件夹下面的zip文件就能看到主界面的东西.暂时用hidden屏蔽掉挡住的显示就行了...额

不过具体在哪配置读取系统时间的东西,还请高人赐教啊= =~

最后感谢观看~图片随意转载但请标明原出处: [原文地址]({{ page.url }})