---
date: 2011-03-09
categories:
  - 未分类
tags:
  - opencv
  - QT
  - windows
  - 记录
title: 在win下面编译opencv
---

今天折腾了好几个小时来弄这个opencv的win驱动啊。。。。各种复杂。。好歹最后还是弄出来了。。。<br />最开始按照教程来编译：<a href="http://www.opencv.org.cn/index.php/Mingw%E7%BC%96%E8%AF%91%E6%9C%80%E6%96%B0%E7%89%88%E6%9C%AC%E7%9A%84OpenCV%E4%BB%A3%E7%A0%81">原文连接点我</a><br />光是在win下面装个svn的软件就比较复杂了。。。好不容易下好之后checkout的时候又发现没有代理下载opencv的svn太慢了。。。又花了大半个小时来弄好vpn代理。。唉~<br />好不容易下好了opecv的svn包。接着安装cmake软件。。又是蛋疼的大半个小时。。。<br />最后发现本来找不到mingw的位置。。但是按理说我装了Qtcreator之后就安装好了mingw的编译器的。。所以在这里和教程有所不同。直接选择qt自带的mingw编译器。<br />在系统路径下修改，添加我自己的安装路径下的mingw/bin。<br />具体的实现可以参照这两篇文章：<br />1.添加系统路径<a href="http://blog.csdn.net/neyes/archive/2010/09/26/5908137.aspx">&nbsp;&nbsp; http://blog.csdn.net/neyes/archive/2010/09/26/5908137.aspx</a><br />2.使修改后的系统路径立即生效 <a href="http://blog.goods-pro.com/?p=146">http://blog.goods-pro.com/?p=146</a><br /><br />之后就可以在cmake下面生成对应的makefile文件了。。最后再调用mingw的编译exe生成所需要的全部文件。<br />注意不同版本的mingwmake文件的不同。名字需要按照自己所使用的版本进行修改