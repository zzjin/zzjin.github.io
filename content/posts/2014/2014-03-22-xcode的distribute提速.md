---
date: 2014-03-22
categories:
  - web
  - xcode
  - publish
  - android
tags:
  - ios
  - xocde
  - application loader
title: xcode的distribute提速
---

今天在给新版的ios客户端上传发布的时候等待了很久...期间有一个多小时一直卡在`is uploading`的界面上.....

一直等啊等,完全没有动静...知道后来网上搜了一下才发现不是网速的问题

<!--more-->

最主要的是查阅到额sf上面的一个神贴: http://stackoverflow.com/questions/18971710/application-loader-stuck-at-the-stage-of-authenticating-with-the-itunes-store/19360043#19360043

里面提到了一种变态的方法,在回答和评论里面大家都惊呼: "尼玛这是啥,尼玛真管用"

我自己也是等待了接近两个小时之后忍无可忍尝试了一下...结果1min不到搞定上传收工回家...

下面列出以下具体的操作步骤: (查阅大量资料之后化繁为简的流程)(自用)

1. 进入Organizer,选择要提交的archive,点击Distribute...
2. 选择第二个Save for XXX,选择皮肉vision的档案,然后生成好ipa备用
3. 进入Application Loader,选择Deliver Your App,按照传统流程选择刚才生成好的ipa文件
4. 在Application Loader里面点击Send
5. 切换回Organizer,选择要提交的archive,点击Distribute...
6. 这次选择默认的第一个Submit to the IOS App Store,选择刚才生成ipade provision,点击upload
7. 这时同时是有两种方式在向iTunesConnect提交数据,几乎瞬间,Application Loader就会提示各种问题
8. Organizer提示上传成功.

不知道是谁发明(发现?)了这种方法..不得不说..真是贱得让人爱不释手...

原文地址: [xcode的distribute提速]({{< ref "2014-03-22-xcode的distribute提速" >}}) 作者 : ZZJIN

转载请注明出处.