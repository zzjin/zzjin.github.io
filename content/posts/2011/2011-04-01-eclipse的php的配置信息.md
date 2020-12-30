---
date: 2011-04-01
categories:
  - 技术
tags:
  - eclipse
  - web
  - 记录
title: eclipse的php的配置信息
---

<a href="http://everhow.blog.163.com/blog/static/3573773200842843250245/" title="原文链接">原文链接</a>
<h3>
	Zend Studio for Eclipse php.ini配置文件的问题(虽然我现在在使用的是XDebug..但是可以尝试一下)</h3>
在使用Zend Studio for Eclipse 时，运行php script时，会出现不能加载extension的问题， 这个问题的关键在于，zend使用的php.ini不是windows下的php.ini,而是他自己的php.ini, 所以解决问题的关键就是修改zend使用的php.ini, 这个文件所在的位置在zend安装目录下的 PHP5 pluginsorg.zend.php.debug.debugger.win32.x86_5.2.12.v20071210resourcesphp5 PHP4 pluginsorg.zend.php.debug.debugger.win32.x86_5.2.12.v20071210resourcesphp4 修改这个文件就可以咯