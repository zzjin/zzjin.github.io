---
date: 2012-07-13
categories:
  - web
  - 技术
tags:
  - ThinkPHP
  - ThinkPHP2.1
  - 模版
  - 记录
title: thinkphp的一个自带模版bug
---

今天在写框架的一个tpl的时候遇到了一个很囧的bug,到了上线才被发现出来...不过解决也很简单

<!--more-->

### 问题的出现

在使用volist的时候,如果设置了offset而没有写length,则在解析的时候就会变成:

```php

$__LIST__ = array_slice($name , $tag['offset'],count($__LIST__),true);

```

问题就出在这个解析上,这个是$__LIST__的第一次赋值,而就在这个里面就对其进行了count操作.可以想到肯定返回的是0..那么array_slice的操作返回的结果只能说返回最后14个数据了= =....

### 问题的解决

知道问题在哪之后就好办了,找到出问题的解析文件"TagLibCx.class.php"当中的解析_volist的函数,把对应的位置修改成array_slice函数本身的方法就好了

```php

$__LIST__ = array_slice($name , $tag['offset'],NULL,true);

```

看吧,确实很简单的就解决了......(喂!)

至少在我们的后续的实际测试当中完美的达成了想要的结果

### 解决之后

不能自己一个人爽一爽...或者说自己爽完了之后得看看现在的框架还有没有这个问题.

在看了2.1.2.2的源码之后发现还是存在这个问题.count一个空值导致的slice失败

建议还在使用2.1与2.2框架的各位如果用到了volist的话简单的fix一下这个小bug.

在最新的trunk的3.X的代码库上 Cx库这个问题已经得到了fix.但是官方的fix是这样的:

官方源码 : http://thinkphp.googlecode.com/svn/trunk/ThinkPHP/Lib/Driver/TagLib/TagLibCx.class.php

```php

$__LIST__ = array_slice($name,$tag['offset'],count($name)-$tag['offset'],true);

```

好吧.两种fix的方式谁好谁简单谁减少系统内存谁XXOOXX.....就不多说了.(笑)

<strong>更新: 由于官方已经把代码迁移到了github,所以我直接提交了patch,也合并到主线了~以后大家下载的版本都有这个fix啦~
(你这货的fix根本就没啥用吧!!!)</strong>