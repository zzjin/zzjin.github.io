---
date: 2011-11-07
categories:
  - git
  - 技术
tags:
  - git
  - git配置
  - win
title: '[zz]Windows下面的git配置'
---

<ol>
	<li>在Git Bash提示符下,使用git add添加含有中文的新文件时乱码(乱码类似：\316\304\261\276\316\304\265\265.txt).
解决方案：编辑D:\Git\etc\inputrc文件中对应的行：
查找以下2行，并修改其值：
原先：
[shell]set output-meta off
set convert-meta on[/shell]

改为：

[shell]set output-meta on
set convert-meta off[/shell]</li>
	<li>在Git Bash提示符下，使用git log查看含有中文的log信息时乱码（乱码类 似：<E4>;<BF>;<AE>;<E6>;<94>;<B9>;<E6>;<96>;<87>;<E6>;<9C>;<AC>;<E6>;<96>;<87>;<E6>;<A1>;<A3>;)
解决方案：
<ul>
	<li>在Bash提示符下输入：
[shell]git config --global i18n.commitencoding utf-8
git config --global i18n.logoutputencoding gbk[/shell]

注：设置commit提交时使用utf-8编码，可避免Linux服务器上乱码；同时设置在执行git log时将utf-8编码转换成gbk编码，以解决乱码问题。</li>
	<li>编辑D:\Git\etc\profile文件，添加如下一行：
[shell]export LESSCHARSET=utf-8[/shell]

注：以使git log可以正常显示中文(需要配合：i18n.logoutputencoding gbk).</li>
</ul>
</li>
	<li>在Git Bash提示符下，使用ls命令查看含有中文的文件名乱码(乱码类似：????.txt)
解决方案：
使用ls --show-control-chars命令来强制使用控制台字符编码显示文件名,即可查看中文文件名.
为了方便使用，可以编辑D:\Git\etc\git-completion.bash文件,添加如下一行:[shell]alias ls="ls --show-control-chars --color=auto"[/shell]</li>
	<li>在Git Gui中查看UTF-8编码的文本文件时乱码(乱码类似:锘夸腑鏂囨枃妗￡).
解决方案:
在Bash提示符下输入:
[shell]git config --global gui.encoding utf-8[/shell]

注：通过上述设置,UTF-8编码的文本文件可以正常查看,所以需要将所有文本文件的编码统一为UTF-8.</li>
</ol>