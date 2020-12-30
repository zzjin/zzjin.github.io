---
date: 2012-06-12
categories:
  - web
  - 技术
tags:
  - bootstrap
  - modal
  - 学习
  - 技术
  - 记录
title: bootstrap学习系列(2)  Modal插件的复用与自动隐藏
---

最近在年初初学bootstrap的基础之上有再一次的在应用中使用了这个框架

对这个框架有了比较深入的了解(其实就是可以更好的应用它而已)

bootstrap包含的部分js确实只能用简单(简陋)来形容(参见[如何让bootstrap的modal支持ajax内容]({{ page_url 2012-04-03-bootstrap学习系列1-让modal支持ajax内容 }}))

这次使用的时候又一次的遇到了js插件的不完善的问题

modal插件就是背景变暗,然后从上到下的的出现对话框.(不得不说对话框的样子设计挺好的).然后你可以直接在这个对话框里面操作

<!--more-->

说具体的需求之前先来看看一段最基础的代码(基于官例简化)

```html

<div class="modal hide fade" id="autoModal">;
    <div class="modal-header">;
        <button type="button" class="close" onclick="$('#autoModal').modal('hide');">;×</button>;
        <h3 id="mTitle">;</h3>;
    </div>;
    <div class="ajaxModel">;
        <div class="modal-body">;</div>;
        <div class="modal-footer">;
            <a href="#" class="btn" onclick="$('#autoModal').modal('hide');">;关闭</a>;
            <!-- 这里可以js添加功能按钮 -->;
        </div>;
    </div>;
</div>;

```

可以看到.在官方的基础之上添加了一些后面要用到的类
#### 思路
有了这个代码放在这之后稍微安心了一点,现在我们可以看看具体的需求是什么,以及我的解决思路(你瞎蛋逼吧!喂!)

model作为提示框,出现了之后可以让我们有一些数据可以查看,并操作(或者无需操作).这时页面如果有多个modal需要显示或者操作的话,需要写多个modal的

[烂尾了]