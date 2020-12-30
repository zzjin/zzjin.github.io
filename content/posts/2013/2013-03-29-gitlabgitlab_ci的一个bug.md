---
date: 2013-03-29
categories:
  - web
  - 个人
  - 技术
tags:
  - bug
  - gitlab
  - gitlab_ci
  - 持续集成
title: gitlab/gitlab_ci的一个bug?
---

今天想在服务器上增加一个项目的ci的时候发现新项目的depoly_key无法加上额(不加上的话没法在服务器pull与checkout代码)

之前的两个ci进程都是好好的啊为啥今天就不能加了,才发现新版的(5.0)的gitlab切换到gitlab-shell之后,把所有的key都放在一起存放,然后再各自简单的分配权限...导致我加服务器的ci的key的时候判断重复冲突了....

好吧,略囧.没办法只能想出一个解决方法:

1. 把之前的几个项目的deply_key都先删掉(所有项目的都得删干净)
2. 新建一个用户叫做 gitlab_ci,给这个用户配置上对应的ci的deploy_key
3. 把需要ci的项目增加一个reporter成员(对项目的只读权限)为gitlab_ci
4. 搞定收工

这种情况只有在一台服务器有多个项目需要ci的时候有用的....来着...

仅供各位看官参考....