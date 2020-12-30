---
date: 2013-10-23
categories:
  - web
  - 技术
  - js
  - jquery
tags:
  - html
  - 正则
  - jquery
  - 插件
  - 丧心病狂
  - 兼容
title: html内容用jQuery实现autoLink插件linkUrl
---

有很多时候我们都需要给一个网页里面的正文部分的url加上超链接,能实际的点击这个链接来增强用户体验

有些时候我们会选择保存富媒体的时候判断,有些时候我们留给js来做这个操作

经过几天的时间的调研和实际项目的数十个页面的实际测试,终于得到了这个自认可以分享的jquery插件'linkUrl'.

<!--more-->

## 特点介绍

1. **直接处理html的内容,不需要预传值**
2. **能够过滤已有的`a`标签的干扰**
3. **符合中文网页环境的url判断**
4. **代码简单耐艹**

## 源码说明

是的你没有看错,所有的实现逻辑和细节我都直接写在源码直接分析了!

```js
//我从jquery扩展中来
(function($) {
    $.fn.extend({
        linkUrl:function() {
            //肯定是用到正则的.这句话判断一段文字是否是http或者https或者mailto开头的,域名,端口与pathinfo都支持
            var preg=/((?:https?|mailto):\/\/(?:www\.)?(?:[a-zA-Z0-9][a-zA-Z0-9\-]*\.)?[a-zA-Z0-9][a-zA-Z0-9\-]*(?:\.[a-zA-Z]+)+(?:\:[0-9]*)?(?:\/.[^\u4e00-\u9fa5\s<'"·×—‘’“”…、。《》『』【】!（），：；？]*)?)/ig;
            //通过contents来获取当前的dom列表,再foreach,作用下面会提到
            $(this).contents().each(function() {
                //线缓存循环的this,备用
                var obj    = $(this),
                //clone一下数据
                    cloned = obj.clone();
                //再去除掉a标签的干扰,不然会匹配到a标签内的数据(用正则规避?效率?)
                cloned.find("a").remove();
                //先获取text再匹配,避免html转义的一些路径
                var matched = cloned.text().match(preg);
                //有匹配到
                if ("undefined" != typeof e && null != e ){
                    //这里先判断下是否是text的dom,如果是的话用一个span包起来(span而已.需要的话后面可以unwrap掉)
                    if (typeof ( a.html() ) == 'undefined'){
                        //娶不到数据,jquery返回undefined
                        obj = obj.wrap("<span />").parent();;
                    }
                    //遍历所有的链接
                    $.each(matched,function(n,match){
                        //$()一个空dom的方式来处理url的显示和转码的问题
                        //这里每次都获取最新的html,获取上一次循环替换之后的html
                        //可以clone,替换clone之后的,最后一次性的写回去增加效率(但是clone会丢失整个selector绑定的事件(?))
                        var watch = obj.html().replace($("<div />").text(match).html(),'<a href="' + match + '" target="_blank">' + match + "</a>");
                        //重写回去
                        obj.html(watch);
                    });
                }
            });
        }
    });
})(jQuery);
//到扩展语法结束
```

#### 直接引入的在线github地址(574字节):

<script async src="https://gist.github.com/zzjin/7116504.js"></script>

## 最后的自嘲

断断续续的花了2天时间来写这么一个破脚本扩展.....不过最后的实用性还不错...唉~

没有专门的花大时间来学js,这个脚本扩展的效率应该还有很大的优化空间,欢迎大家的建议!

如果有任何建议--意见--西瓜皮--口水啥的(喂)  **热!烈!欢!迎!吐!槽!**

原文地址: [html内容用jQuery实现autoLink插件linkUrl]({{< ref "2013-10-23-html内容用jQuery实现autoLink插件linkUrl" >}}) 作者 : ZZJIN

转载请注明出处.