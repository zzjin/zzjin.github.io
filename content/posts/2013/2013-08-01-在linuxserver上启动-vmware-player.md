---
date: 2013-08-01
categories:
  - linux
  - 技术
tags:
  - nogui
  - vmware
  - vmwareplayer
  - 虚拟机
title: 在linuxserver上启动 vmware player
---

最近由于搬家的原因几个服务器都进行了迁移.其中gitlab的服务器从原来的运行在winserver2008上迁移到了一个没有界面的ubuntuserver上

<!--more-->

总的来说给新的ubuntu server装上vmware player没有任何的问题,按照提示用sudo权限直接装就行了..

但是装好之后没有任何的命令能启动复制过去的虚拟机镜像..默认的vmplayer命令是打开一个gtk的gui界面.显然服务器没有那么多乱七八糟的东西.

在网上搜索了一下,大部分都是讲如何在gui(xserver)点击什么,然后点击什么的.....

坑爹啊这是!没有图形界面怎么让老子玩啊!

继续搜了下之后发现官方给出了一个叫vmrun的命令行,可以简单的让player执行一些命令...

但是坑爹的wmplayer安装之后没有默认的vmrun的cli...还得自己去下载一个叫vmware-VIX的sdk一样的东西....

总结安装方法就是:

**新linux的server->;安装vmwareplayer->;安装vmware-vix-api->;执行命令**

(两个软件都在官网的一个页面下载:比如这个:
\([download vmware](https://my.vmware.com/web/vmware/free#desktop_end_user_computing/vmware_player/5_0)\)
两个都装好之后就可以用下面的方式简单的管理虚拟机了:

```bash
vmrun -T player start|stop|restart  /path/to/vm/some.vmx nogui
```

执行之后相应的虚拟机就跑起来了..但是这种方式目前还不能支持gui下面的修改虚拟机的配置参数啊,更改网络连接,查看对应ip或者其他之类的东西..不如说除了刚才给出的三个之外啥都干不了...)

不过也算是在一个没有gui的环境下跑起来了免费的不用破解或者盗版的虚拟机吧.....至于其他的功能...用别的带gui的host打开虚拟镜像,编辑参数,再保存,再提交到server重新开始运行就好了= =.....