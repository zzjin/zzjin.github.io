---
date: 2013-09-04
categories:
  - 个人
  - 技术
  - sublimetext
tags:
  - 杂谈
  - 备忘
title: sublimetext的project忽略部分文件夹
---

很多时候项目有编译目录,编译目录一遍都在当前项目里面,而在sublimetext里面用ctrl+p打开文件或者ctrl+shift+f搜索文件的时候都会打开这些目录下的文件造成一定的干扰.

好在sublime直接支持perproject的设置exclude目录.

打开项目之后选择'Project' -> 'Edit Project', 在打开的文件里面加上一句话:"folder_exclude_patterns": ["_site"],其中_site改成想要忽略的文件夹名称

注意每行后面的逗号,保存就能直接看到效果了.

记录下备忘