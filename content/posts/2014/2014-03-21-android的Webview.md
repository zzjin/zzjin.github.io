---
date: 2014-03-21
categories:
  - web
  - 技术
  - android
tags:
  - html
  - webview
  - android
title: android的Webview
---

时隔很久终于想起来更新下blog...

在使用android的webview控件的时候有非常详细的教程(官方的)

一般来说就是加载一个webview的view然后初始化他,接着配置什么js启用啊,url的引导什么的...

不过在实现点击尸体返回按键返回网页的时候出现了一点问题.

<!--more-->

一般按照网上和官方的说法:

```java
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
    // Check if the key event was the Back button and if there's history
    if ((keyCode == KeyEvent.KEYCODE_BACK) && myWebView.canGoBack()) {
        myWebView.goBack();
        return true;
    }
    // If it wasn't the Back key or there's no web page history, bubble up to the default
    // system behavior (probably exit the activity)
    return super.onKeyDown(keyCode, event);
}
```

简单的判断是否是返回按键且webview有访问历史就调用webview的返回操作.


在一开始测试的时候没有问题,但是在不同网络下做测试的时候发现有些奇怪的反馈

问题就在goBack这个函数不是block的,当网页加载需要时间很长(图片较多)的时候,goback会瞬间返回,等到整个页面加载完成才会响应这个调用

如果用户心急连点几次的话页面短时间内不会有任何变化,导致可能觉得程序卡死了...

网上有别的说法说可以写一个mediadownloader的类来单独下载图片,但是没有区别,在开始下载图片为止整个webview都会按照这个逻辑处理

找了很久才发现是页面等待加载的这个问题,
在官方文档里面也没有提到goback的触发机制和执行时间问题...

最后修改之后代码如下:

```java
if ((keyCode == KeyEvent.KEYCODE_BACK) && myWebView.canGoBack()) {
        myWebView.stopLoading();
        myWebView.goBack();
        return true;
    }
```

这样就在每次goback之前停止正在加载的之前的内容,然后触发返回操作,系统就会正确的响应用户的按键

最后: android真心是个坑,在调试的时候想了很多可能的问题,最后才找到问题的关键,希望能对大家有所帮助,免得走弯路

最后的最后: ios的webview没有这个问题,默认就是会停止加载当前的以便能直接响应用户的操作(良心)

原文地址: [android的Webivew]({{< ref "2014-03-21-android的Webview" >}}) 作者 : ZZJIN

转载请注明出处.