---
title: "React前后端分离在阿里云的oss+cdn的配置"
date: 2019-06-18T18:04:25+08:00
draft: false
toc: true
images:
tags: 
  - aliyun
  - oss
  - cdn
  - react
---

### 需求说明
使用react编译好的静态资源，index.html部署到网站首页，同时其他静态资源部署在传统的cdn上面

### 需求拆解

1. react的webpack打包的时候拆分production的html与静态资源的加载路径
2. index.html的cdn配置

#### webpack打包配置

项目目前使用的是cra，所以需要使用环境变量`PATH`的方式去控制。核心参数是`PUBLIC_URL`，同时在prod的时候可以关闭默认的`GENERATE_SOURCEMAP`。

#### cdn作为前后端分离的静态网站的回源设置

新建一个cdn与oss  
其中oss只有一个index.html文件，同时开启oss的静态网站托管模式，设置默认首页到这个index.html  
配饰cdn，常规配置不再阐述，核心是设置cdn的`缓存配置`：  
1. 设置缓存过期时间 地址是所有地址`/`，国旗时间设置为0
    > 说明：虽然设置了不缓存，但是cdn还是有etag和hash等返回头，浏览器回请求一次接口，但是回返回304
2. 设置重写 待重写URI配置为`^/.+$`，目标URI是`index.html`，执行规则选break
    > 这里的正则是把所有/下面的自路径全部重写到index.html让js去处理实际渲染的页面

之后配置ci每次打包自动更新到对应的cdn就好了


### TODO： oss的上传脚本


