---
date: 2011-09-16
categories:
  - git
  - web
  - 技术
tags:
  - git
  - pull
  - 记录
  - 转载
title: '[ZZ]git pull 的时候总提示出错'
---

<p >最近在一个小组里面搞协同开发,但是我搭建的git服务器配置好之后却遇到了一些奇怪的问题.困扰了我一段时间的就是git的pull没有默认的值的情况,在网上找到了很好的解决方法,现在放在这里...</p>
<p ><!--more--></p>

<h1 >git pull的默认地址</h1>
<div id="cnblogs_post_body">

当git clone之后，直接git pull它会自动匹配一个正确的remote url

是因为在config文件中配置了以下内容：
<div>[text]
[branch "master"]
 remote = origin
 merge = refs/heads/master
[/text]

</div>
表明：
<ol>
	<li>git处于master这个branch下时，默认的remote就是origin；</li>
	<li>当在master这个brach下使用指定remote和merge的git pull时，使用默认的remote和merge。</li>
</ol>
但是对于自己建的项目，并用push到远程服务器上，并没有这块内容，需要自己配置。

如果<span >直接运行git pull</span>，会得到如此结果：
<div>[bash]
$ git pull
Password:
You asked me to pull without telling me which branch you
want to merge with, and 'branch.master.merge' in
your configuration file does not tell me, either. Please
specify which branch you want to use on the command line and
try again (e.g. 'git pull <repository>; <refspec>;').
See git-pull(1) for details.

If you often merge with the same branch, you may want to
use something like the following in your configuration file:

[branch "master"]
remote = &amp;lt;nickname&amp;gt;
merge = &amp;lt;remote-ref&amp;gt;

[remote "<nickname>;"]
url = <url>;
fetch = <refspec>;

See git-config(1) for details.
[/bash]

</div>
在参考[2]中，有这样一段：

Note: at this point your repository is not setup to merge _from_ the remote branch when you type 'git pull'. You can either freshly 'clone' the repository (see "Developer checkout" below), or configure your current repository this way:
<div>[bash]
git remote add -f origin login@git.sv.gnu.org:/srv/git/project.git
git config branch.master.remote origin
git config branch.master.merge refs/heads/master
[/bash]

</div>
因此通过git config进行如下配置：
<div>[bash]
$ git config branch.master.remote origin
$ git config branch.master.merge refs/heads/master
[/bash]

</div>
或者加上--global选项，对于全部项目都使用该配置。

参考：
<ol>
	<li><a href="http://stackoverflow.com/questions/658885/how-do-you-get-git-to-always-pull-from-a-specific-branch">How do you get git to always pull from a specific branch? - Stack Overflow</a></li>
	<li><a href="http://savannah.gnu.org/maintenance/UsingGit">Maintenance Docs UsingGit</a></li>
</ol>
</div>
最后放上原文地址:<a href="http://www.cnblogs.com/lbsx/archive/2010/10/16/1853193.html" target="_blank">http://www.cnblogs.com/lbsx/archive/2010/10/16/1853193.html</a>