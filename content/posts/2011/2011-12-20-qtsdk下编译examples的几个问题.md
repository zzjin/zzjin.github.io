---
date: 2011-12-20
categories:
  - QT
  - 个人
tags:
  - QT
  - 编译
title: QtSDK下编译Examples的几个问题
---

今天在尝试更新了SDK下面的Exalmpes文件之后,尝试在QTC环境下编译整个examples.pro项目,速度是比较慢的说.

遇到了一个比较囧的问题.
<ol>
	<li>由于sdk的安装方式,默认安装的话少了一些PATH的导入....导致重新下载的Examples项目会提示一些找不到的库,路径之类的
<ul>
	<li>$$[QT_INSTALL_EXAMPLES] 这个路径应该是在安装的时候由安装文件配置好的= =但是会提示找不到路径.不过在编译过程中不会出现错误就是了.可以无视</li>
	<li>$${QT_BUILD_TREE}这个的预定义只在Examples/tools/plugandpaint/plugandpaint.pro里面出现了.原文是这样的:

```cpp
symbian {
   LIBS           = -lpnp_basictools.lib
} else {
    LIBS           = -L$${QT_BUILD_TREE}/examples/tools/plugandpaint/plugins -lpnp_basictools
}
```

而这个变量在sdk安装的时候没有预定义好.(至少在我的win32-mingw版本sdk是这样的).最后面说明需要加载的pnp_basictools这个库是同时编译生成的.如果正确的话确实应当放在tools/plugandpaint/plugins这个目录下.但是由于$${QT_BUILD_TREE}不存在.导致最后编译器查找的位置在/examples/tools/plugandpaint/plugins.实际地址为C:/QT安装路径/Examples编译路径/tools/plugandpaint/plugins下面.所以解决方法就有两种:
(1) 设置QT_BUILD_TREE路径,指向正确的自己的编译路径.
(2) 遇到错误时需要的plugin其实已经编译好了.所以将编译好的plugin放入sdk的qt库的lib文件夹里面也可</li>
</ul>
</li>
	<li>接着出现了qaxwidget.h不存在的问题.ax的问题一般是不想管的(因为我用不到).不过看了一下出现问题的头文件,发现不是Examples的问题.而是sdk下载的include下面的qaxwidget.h不是实际的需要的头文件.而是指向src目录下面的对应文件.但是sdk提供的bin环境并没有包括这个src文件夹.简单处理,将sQtSources下面的src文件夹复制到预编译下面就好~</li>
	<li>整个ax都有多少多少少的问题.所以解决方法是:删除demo的ax部分= =.....等以后有需要了之后再慢慢分析问题并解决吧~</li>
</ol>
最后总结一句话:其实自己编译不如专门再去下个qt的最新lib.lib的安装包会提供完整的例子和demo的已编译程序.....想要不蛋疼的话就去安装个lib吧~