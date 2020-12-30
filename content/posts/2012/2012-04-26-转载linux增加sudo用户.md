---
date: 2012-04-26
categories:
  - linux
  - 技术
tags:
  - user
  - 权限
  - 记录
title: '[转载]linux增加sudo用户'
---

原文链接:<a href="http://www.chinasb.org/archives/2011/07/2919.shtml" target="_blank">http://www.chinasb.org/archives/2011/07/2919.shtml</a>

修改/etc/sudoers文件，修改命令必须为visudo才行

```bash

visudo -f /etc/sudoers

```

在root ALL=(ALL) ALL 之后增加

```bash

zzjin  ALL=(ALL) ALL

Defaults:zzjin timestamp_timeout=-1,runaspw

# 增加普通账户zzjin的sudo权限

# timestamp_timeout=-1 只需验证一次密码，以后系统自动记忆

# runaspw 需要root密码，如果不加默认是要输入普通账户的密码

```

修改普通用户的.bash_profile文件(vi /home/tom/.bash_profile)，在PATH变量中增加

```bash

/sbin:/usr/sbin:/usr/local/sbin:/usr/kerberos/sbin

```