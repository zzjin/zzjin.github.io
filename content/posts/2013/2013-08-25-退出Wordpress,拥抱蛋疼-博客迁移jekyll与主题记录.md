---
date: 2013-08-25
categories:
  - 个人
  - 技术
  - 主题
tags:
  - theme
  - wordpress
  - exit
  - jekyll
  - 迁移
  - 静态博客
  - 蛋疼
title: 退出Wordpress,拥抱蛋疼-博客迁移jekyll与主题记录
---

上周开始脑子又抽风了...把用了快两年的wordpress博客迁移到了静态的jekyll编译的静态博客.并且托管在了[gitcafe](https://gitcafe.com/)上面,域名保持不变

于是又是好一番折腾...

顺便还写了一个主题,也在这里说明下吧

终于是自己基本上重写整个主题(原主题:<a href="http://pizn.github.io/blogTheme/" target="_blank">The One</a>)

说下改变之后的理念吧:

*居中显示所有内容* ,之前的主题不太适合大屏幕下面的浏览,所有内容都相对的便宜到了左边,强迫症表示歪着头哪里怪怪的,所以就把整个布局都迁移到了居中显示

*流布局的分别实现* ,由于有个显示器是1080*1920的分辨率,所以在去除滚动条之后宽度只有1000+的浏览器下修改了自适应的宽度与实现方式,当大于1000像素的时候居中显示,低于1000的时候自适应宽度,低于640宽度的时候铺满整个页面

*tag的支持* ,从wp迁移过来的时候转换的post带了很多以前的tag标签,使用了[jekyll/tagging](https://github.com/pattex/jekyll-tagging)之后,简单的调用就能显示当前页面的tag(们),点击tag名字能进入页面,但是没有统一的所有tag的全局入口(样式不知道怎么拼接,既不喜欢wp的那种3d效果,也不喜欢wp默认的标签云...)

*About* ,全新写的 About/Owner/Author/Me 页面,能够通过配置里面的github用户名,自动拉取对应的拥有的repo和做出过贡献的repo,具体参考的就是github的openApi,样式的话使用的是`githubRepoWidget`js插件,最终效果还不错...

*代码高亮* 基本就是调用的gituhb的css渲染的实际的东西(明明写了line-number但是没显示出来囧)

*功能维持原状* 之前的wp的绝大多数页面使用相关的功能都得到了保留,比如tag,category(暂未展示),评论,rss(暂未支持)都得到了保留,而我之前blog加载的cache,allinone之类都没用了