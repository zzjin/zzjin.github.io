---
date: 2012-06-19
categories:
  - 个人
tags:
  - 记录
  - 随笔
title: wordpress 更新
---

好久没更新blog了= =

自从那啥之后也没有以前那么多的精力可以折腾全部的新东西了....

这次只是个记录的文章,在升级本博客到3.4的时候出现了一些问题

不知道啥时候开始.vps的ftp就崩溃了.一直启动不起来.也不报错...

重装了pureftp整个软件也不行..额.

但是wordpress的更新却一直提示我需要ftp命令.

然后google了一下.还是找到了不需要ftp的解决方法:

```php
define("FS_METHOD", "direct");
define("FS_CHMOD_DIR", 0744);
define("FS_CHMOD_FILE", 0744);
```


(注意权限不要给得太高了...)
让拥有者WWW有权限,其他的只能读取,就可以了
然后..然后就妈妈再也不用担心我的升级了...

其实这个blog就是纯水+刷存在用的= =