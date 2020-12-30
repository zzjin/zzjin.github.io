---
date: 2013-08-24
categories:
  - linux
  - 技术
  - web
tags:
  - nogui
  - apache
  - php
  - upgrade
title: ubuntuapache2.2升级到2.4的若干问题与解决
---

由于项目用到的外部依赖越来越多.就萌生了把开发团队的代码编译与执行进行区别与统一的想法,于是想到了用文件共享来实现本机编辑代码,linux统一内网服务器执行的方案

周一买了个新的域名就开始直接配置,最开始的windows文件共享,linux直接mount的过程都非常顺利,大家都是内网,ls和执行的速度都非常的不错

nginx优势就是配置非常灵活,对vhost的支持也是非常的到位,直接用if里面就能指向特定的变量代表的root目录了..真的是一两行走遍天下....

问题其实很简单就出在了apache这个老头子身上

## 需求与问题描述

需求其实很单一:就是简单的二级域名泛解析,把(.*).text.com的域名解析到/home/share/$1/这个根目录下面,之后的请求都按照这个root来相对定位.

dns和nginx代理都非常简单,一下子就设置好了,但是apache找来找去支持的配置就只有那一个

apache的配置需要vhost的支持,关键的参数就是:

```ini
UseCanonicalName Off
VirtualDocumentRoot "/home/share/%1/"
```

* 第一个问题来了... apache默认没有编译'[mod_vhost_alias](http://httpd.apache.org/docs/2.2/mod/mod_vhost_alias.html)'.....

<!--more-->

## 漫漫解决之道

既然apache没有编译,那我们自己编译就好了嘛...(这个时候还在幻想直接加载一个动态模块就能解决)

按照网上的教程,找到模块的文件,进行编译

```bash
sudo /usr/local/apache/bin/apxs -i -a -c /path_to_apache/modules/mappers/mod_vhost_alias.c
```

就自动生成了需要的'mod_vhost_alias.so',在配置文件加载之后

却发现 **完全** 没起作用,根部没法加在,不是显示没有权限,就是显示无法索引目录..不停的找啊找,终于在 **[github](https://github.com/mhlavac/lamp-install-script/issues/2)** 上发现了问题所在...

* 第二个问题来了... apache2.2的一个bug

我们的项目在项目目录有.htaccess文件,里面有一个rewrite规则,将全部的请求都重定向到index.php(全部静态文件都走nginx直接或者缓存返回了)

而这个bug就是apache2.2下面不能同时支持'VirtualDocumentRoot'和Rewrite配置!

我就擦了...尼玛这个[bug](https://issues.apache.org/bugzilla/show_bug.cgi?id=26052)很严重啊!!!官方的链接下面提到了他们很不要脸的在2.4版本才fix了这个问题....

想来想去,看来在2.2的时候是没法好好的支持这个了...那就一不做二不休升级apache2.4吧!

这才是悲剧的真正开始.....(为了达到目的!)

升级apache本身没什么问题,下载源码.直接按照老的参数编译就好了.

编译好了之后....

当然是什么都没反映,连conf文件都加息不过!!!

* 第三个问题来了 apache2.4的各种upgrade须知

官方在比较明显的位置放置一个 **[网页](https://httpd.apache.org/docs/trunk/upgrading.html)**

里面详细的描述了升级到2.4是一件多么让人想 摔盘子|撇晾衣杆|大骂坑爹 的过程..

一般lamp或者lnmpa架构相关的变化可以归纳成两点:
1. 配置文件的变化
  配置文件中的Auth的默认配置修改与Order,Allaw的语法修改,不过这块可以通过加载一个回滚版的模块来避免修改
2. 模块加载方式的变化
  这个才是最大的坑爹,apache2.2从之前的把全部模块编译在一起改成了编译富哦个常用模块然后按需加载的方式了.导致老的配置文件会报各种配置错误(一般都是因为没有紧挨在对应的模块)
  经过一个个的测试与调查,我总结了下面的需要在conf里面手动load的模块列表

```ini
# 下面的是原来的只用加载php5的模块
LoadModule php5_module          modules/libphp5.so
# 下面的是新编亿2.4之后的手动增加的模块
LoadModule unixd_module         modules/mod_unixd.so         # User和Group关键字的支持
LoadModule rewrite_module       modules/mod_rewrite.so       # rewrite引擎模块
LoadModule access_compat_module modules/mod_access_compat.so # 支持老的Orsedr,Allow语法(第一条fix)
LoadModule mime_module          modules/mod_mime.so          # mime,如果apache除了返回php之外还可能动态返回json啊,jsonp啊,缩图功能等,就要加载这个
LoadModule vhost_alias_module   modules/mod_vhost_alias.so   # 恨死你了
LoadModule alias_module         modules/mod_alias.so         # 基础的server alias等的模块,用来支持*.test.com的语法
LoadModule negotiation_module   modules/mod_negotiation.so   # AddEncoding语法
LoadModule autoindex_module     modules/mod_autoindex.so     # 有时候有用到
LoadModule log_config_module    modules/mod_log_config.so    # CustomLog语法的支持
LoadModule authn_core_module    modules/mod_authn_core.so    # 支持老版的AllowOverride语法
LoadModule authz_core_module    modules/mod_authz_core.so    # Auth None也要加载这个module,擦了
LoadModule dir_module           modules/mod_dir.so           # 项目目录下面的.htaccess文件的好基友
```

基本上加载了这些模块之后就没有bug了.如果还有bug的话可以按照出错的提示搜索,一般在来说就是某些配置的小改动或者再按照需求加载一些模块就是了

终于apache运行起来了!!!

本以为apache跑起来之后,php也重新编译了.php5.so也生成好了.插件也都编译了...

但是.....

* 第四个问题来了... 尼玛apache不执行php!!!!

直接访问test.php文件竟然把文件按照txt给老子显示出来了!尼玛要是在线上岂不是全部暴露了所有源码!!!擦擦擦!

在网上一顿乱搜之后还是找到了解决方法: http://stackoverflow.com/questions/7264014/why-is-my-php-source-code-showing

需要在conf的奇怪的角落加上一段话:

```ini
AddModule mod_php.c
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
```
似乎是让apache知道XX类型文件用XX方式加载...唉

配置,重启,打开test.php...终于执行了....OMG!

* 第五个问题来了... 尼玛无法直接用域名访问页面了!

如果访问X.test.com/test.php就是好的.但是直接访问x.test.com/就会提示没法index目录...

我擦啊.好端端的为啥要去索引目录下的全部文件呢...大哥我写了.htaccess文件的啊!

在配置里面也写了

```ini
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>
```

您老人家为啥就是不找找亲爱的index.php文件呢!

不得已只能再次去官网找对应的模块的文档了:

http://httpd.apache.org/docs/2.2/mod/mod_dir.html

https://httpd.apache.org/docs/trunk/howto/htaccess.html

除了官方建议rewrite能写在conf的就别写.htaaccess里面之外真心没找到原因...

后来不停的实验,最后在.htaccess文件里面加上了一句<code>DirectoryIndex index.php index.html</code>才全部问题得到了解决....

至此.大的apache升级历程就结束了..升级之后基本没有原则上的问题出现...

加载的模块也少了,占用的性能也少了,渲染php的速度也变快了(多半是心理作用)....腰不酸腿不疼了!一口气能打开5个页面了!

#### 解决问题section中最后的一点说明

如果您用的lnmpa方式,apache只监听127.0.0.1,php请求都是nginx转过来的话.有一个好消息,那就是:

mod_rpaf不用再单独编译加载了!apache2.4原生支持了proxy的这部分功能.(以上只能是聊胜于无)

(以上全部命令仅供参考吗,请按照实际路径|方法修改后执行)

## 总结

这次迁移的代价还是比较大的(一整个晚上+第二天的零碎时间)..

* apache的重新编译的各种问题
* php的重新编译
* apache的配置的更新
* phphandle的处理

几乎翻遍了网上的所有的apache的升级教程与各个模块的官方文档...

踩了无数的地雷...

如果没有我这么奇葩需求的请 **务必** **千万** **绝对** **死也** 不要升级apache.....