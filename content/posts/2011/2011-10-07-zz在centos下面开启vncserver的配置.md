---
date: 2011-10-07
categories:
  - linux
  - 技术
tags:
  - centos
  - vnc
  - vncserver
title: '[ZZ]在centos下面开启vncserver的配置'
---

转自:<a href="http://hi.baidu.com/elric0712/blog/item/743be29a0b1c7eafc9eaf436.html" target="_blank">http://hi.baidu.com/elric0712/blog/item/743be29a0b1c7eafc9eaf436.html</a>

声明:一般我转发的东西都会按照wordpress的格式一句话一句话的修改的.绝对不会出现直接复制粘贴的情况!也是为了其他人看着更舒服...

<!--more-->
<h2>centos 的 vnc 服务开启</h2>
如何远程控制centOS桌面? 如何使用windows远程控制centOS桌面?
<ol>
	<li>查看本机是否有安装vnc（centOS5默认有安装vnc）[shell]rpm -q vnc vnc-server[/shell]

如果显示结果为：

[text]
package vnc is not installed
vnc-server-4.1.2-14.e15_3.1
[/text]

那恭喜你，机器上已经安装了vnc，如果没有，就得自己安装了，这里不说怎么安装了，很简单，在centOS的软件库中搜索，点击安装</li>
	<li>把远程桌面的用户加入到配置文件中
[shell]vi /etc/sysconfig/vncservers[/shell]

使用vi编辑器打开配置文件，在文件中添加下面两行命令

[shell]VNCSERVERS="1:root"           --指定远程用户

VNCSERVERARGS[1]="-geometry 1024x768"      --指定远程桌面分辨率
[/shell]</li>
	<li>给你刚刚设置的远程桌面用户 root 设置密码
[shell]vncpasswd[/shell]</li>
	<li>开启VNC端口
[shell]vi /etc/sysconfig/iptables[/shell]

使用vi编辑器打开配置文件，在文件中添加下面一行命令:

[shell]-A RH-Firewall-l-INPUT -p tcp -m tcp --dport 5900:5903 -j ACCEPT[/shell]</li>
	<li>重启防火墙[shell]service iptables restart[/shell]</li>
	<li>修改远程桌面显示配置文件
（不修改此文件你看到的远程桌面很简单，相当于命令行操作，为了远程操作如同本地操作一样，务必参考以下方式进行修改）
[shell]cd ~/.vnc/vi xstartup[/shell]

使用vi编辑器打开配置文件，并进行下列修改:

[shell]#xterm -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop"
#twm
#将以上两个注释
gnome-session  --添加它
[/shell]</li>
	<li>看了这段代码，大家应该明白是怎么回事了</li>
	<li>启动vnc服务[shell]/sbin/service vncserver start [/shell]</li>
	<li>远程连接打开vnc客户端，server框中输入ip:1 (1代表上面配置的远程用户代号，配置文件中可以配置多个远程用户)，这时你便可以轻松的通过友好的远程桌面来控制centOS了。</li>
	<li>开机自动启动vncvi /etc/rc.d/rc.local使用vi编辑器打开配置文件，并进行下列修改/etc/init.d/vncserver start   --新增行</li>
</ol>