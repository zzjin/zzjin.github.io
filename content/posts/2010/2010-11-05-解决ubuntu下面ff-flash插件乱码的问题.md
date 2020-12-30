---
date: 2010-11-05
categories:
  - linux
tags:
  - firefox
  - flash
  - linux
  - ubuntu
title: 解决ubuntu下面FF Flash插件乱码的问题
---

ubuntu/linux flash中文乱码 的解决

打开配置文件：

```bash
cd /etc/fonts/conf.d/
sudo gedit 49-sansserif.conf
```

修改edit节点，将'sans-serif'改为:

```
sans

sans-serif

serif

monospace

sans-serif #这里改为<--- sans
```