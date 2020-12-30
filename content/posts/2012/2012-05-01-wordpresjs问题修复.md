---
date: 2012-05-01
categories:
  - web
  - 个人
  - 技术
tags:
  - theme
  - Transform
  - wordpress
  - 主题
  - 优化
  - 记录
title: wordpresjs问题修复
---

五一期间一直再做一个很好的wp主题的修改.

这篇blog是这个修改系列的其中一个.

本次遇到的问题是自己写的主题用到的代码执行有问题.实际代码执行的时间并不准确.导致显示出现了很奇怪的问题.

<!--more-->

先描述一下原来的使用:

原来在主题的footer.php里面用下面的方式引入了自己的js:

```html
...
<script  src="<?php bloginfo('template_url');?>;/js/Oneirojs.js">;</script>;
...
```

然后在这个js文件里面有这样一个优化:

```js

jQuery(document).ready(function($){
...
var _leftheight = jQuery("#content").height();
_rightheight = jQuery("#sidebar").height();
if(_leftheight >; _rightheight ) {
    $("#sidebar").height(_leftheight);
}
else {
    $("#content").height(_rightheight);
};
...

});
```

先不说这段js本身有的那个小BUG,其主要目的是实现两栏保证最大高度.但是在实际页面调用的时候会出现问题.

主要体现在主题的footer.php加载完成的时候,实际的整个页面并没有加载完成..而这个时候加载在footer的脚本却直接执行了jQuery.ready()里面的方法

对于绝大多数代码都是没有什么问题的.但是就在上面的地方会出现获取#content错误的问题.获取到的不是内容最终的高度

就会出现下面的很囧的现象:

为了美化的代码却导致了效果出不来= =那叫一个囧啊...

幸好有强大如wp的wp(喂!)

搜了一番文档之后找到了解决方法(不得不赞一下wp的在线文档= =~给力)
<ol>
	<li>首先,去掉在footer加载的这个脚本.直接把这行删了就可以.</li>
	<li>接着在主题的functions.php文件最后加上几句话:

```php
//插入插件自己的js文件
function transform_js() {
    wp_enqueue_script('transform', get_bloginfo('template_directory') . '/js/transform.js', false, '0.0.1');
}

//在页尾插入脚本文件
add_action('wp_footer', 'transform_js');

```

简单说一下这是怎么个流程和好处.
<ul>
	<li>创建有一个函数向wp的脚本队列插入自己的主题js文件</li>
	<li>在wp的页脚出注册一个这个函数的事件</li>
</ul>
好处:
<ul>
	<li>wp_enqueue_script这个函数能将需求的文件写入wp管理,并且可以管理版本和加载的先后顺序!depends什么的最好了(虽然在这里没用到)</li>
	<li>acc_action想页脚注册了这个新的函数.使得实际加载的位置还是修改之前的那个地方</li>
</ul>
</li>
	<li>完了</li>
</ol>
解决方法就是这么简单~希望这次的修改能给大家提供线修复bug的思路与联想~

<strong>原创文章 转载请注明出处!谢谢~</strong>