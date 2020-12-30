---
date: 2011-09-15
categories:
  - web
  - 技术
tags:
  - babelzilla
  - firefox
  - mozilla
  - sqlite
  - sqlite-manager
  - 插件
  - 汉化
title: 20110915 firefox插件sqlite-manager汉化
---

<h2 >写在前面的口水</h2>
中午吃了闲得得很蛋疼..所以翻起了陈年老账看看firefox还有没有什么插件可以让我玩一玩..其实事情的起因是我被坑爹的金山快盘的同步搞得很没有耐心...所以就去翻了一翻他的系统目录结构以及实现原理..好吧其实质看了看安装目录和同步文件夹下面的事情,就在同步目录下面发现了一个好东西~

[singlepic id=739 w=600 h=240 mode=watermark float=center]

嗯.一个隐藏的还给了系统保护的文件夹.还尼玛前面加了一个"."...说明不能在windows资源管理器直接修改的..这你是想闹哪样啊!!!.嗯.其这不是重点...点进去之后有一个db文件和一个.cache的文件夹..好吧终于说道重点了..一个名字叫做"klivestate.db"的文件..我一看就笑了..心里就在暗爽啊..你小子金山的数据库被老子逮到了吧..哈哈.绝对是用sqlite保存的!(也不知道你为什么有这么准确的直觉...)

额.好吧前情提要就说到这里...(废话好多...(反正又没人看!!!)),然后就想用一个sqlite的管理器来尝试打开这个文件,当然就想到了大名鼎鼎的firefox,下面的sqlite-manager插件了...直接在插件管理界面装好之后直接尝试打开这个db文件..好吧,直接就打开了...一点难度都木有啊老师!能不能加密?能不能?有木有任何的保护措施?嗯?有木有?还话说官网号称采用"美国军方的AES-256加密算法"..给一个数据库加密就这么困难?还是说你们开发团队都觉得只要数据在网络传输的时候加密了就可以了?不用考虑本地的安全环境?..嗯..小小的吐槽了一下...不过db数据库打开之后看到的表结构还是能学习学习别人好的数据库设计的..嗯嗯.截个图吧.也不是什么太难的技术...

[singlepic id=740 w=600 h=480 mode=watermark float=center]
<p >可以看到数据结构还是设计得很不错的..不过分析具体的表的结构与内容以及和系统之间的联系不是今天这片博客的主旨...so.开正式进入话题...<!--more--></p>

<h2 >第一部分 查找或自己进行中文翻译</h2>
说到ff的插件sqlite-manager..真的是很好用啊....用来管理sqlite的东西确实很不错的.功能各种强大啊.在这里就不细说了.需要下载的可以看这里:<a title="sqlite-manager" href="https://addons.mozilla.org/addon/sqlite-manager" target="_blank">[sqlite-manager]</a>

一般我都喜欢将他作为一个新的窗口打开而不是新标签页,这样不影响ff浏览器本身,正常情况下打开默认显示的是英语,好吧,问题出来了.英语啊...虽然都是很简单的东西,看起来也没什么问题,但是本着天生想折腾的想法..去追究了一下这个这么热门的扩展为什么没有汉化?如果没有汉化的话我也可以帮着搞定他...

[singlepic id=751 w=600 h=480 mode=watermark float=center]

首先进入到官方的code托管页面:<a href="http://code.google.com/p/sqlite-manager/" target="_blank">http://code.google.com/p/sqlite-manager/</a>
发现首页上就显示了目前支持的语言:"Version 0.7.5 supports English, German, Japanese, French, Russian, Spanish, Swedish and Italian languages"
很好.一开始我也不抱希望官方的有汉化的选项.
然后就是在这个页面到处逛逛,最先到了downloads页面,发现木有什么特殊的专门的语言扩展包下载.好吧.继续.接着看到了wiki页面,眼睛一下亮了.竟然有一个topic就叫做"其他语言":

[singlepic id=745 w=600 h=480 mode=watermark float=center]

这个真的是很给力啊..而且点进去之后竟然正好有人问了一句,"我想把它翻译成XXX语言,怎么联系"之类的话,然后回答是" Please join babelzilla to provide the translation. That is where all the translations come from. ",让加入一个什么叫做"<strong>babelzilla</strong>"的东东,之前没听过......
好吧先放在这.再看看别的地方....

在source界面里面可以浏览目前的最新代码..第一眼就发现了一个叫"locates"的文件夹..真棒...直觉告诉我,一般的翻译都在这里面啊~~~
果不其然啊.进去一看,非常明显:

[singlepic id=748 w=600 h=480 mode=watermark float=center]

直接把整个文件夹都下下来了...ok.每一个都点开看看..不看不知道,一看吓一跳啊.之前的兴奋瞬间减少了一半...:
中文目录下面的翻译文件打开基本都是空的啊:

[singlepic id=743 w=320 h=480 mode=watermark float=center]

而相比之下日文翻译的文件打开是这个样子的:

[singlepic id=747 w=600 h=480 mode=watermark float=center]

差距很明显啊..
通过对两个一样的翻译文件的对比发现,其实就是每行后面的引号内的翻译文字的差别啊,既然文件都是一样的话,我自己也能翻译啊...嗯嗯!
正准备自己动手的时候突然又想到了刚才wiki的那句话,"我们的全部翻译都来自那个什么"babelzilla"的网站".嗯,不管三七二十一的先上去看看,如果可以的话在网上翻译了还可以直接提交给sqlite-manager的团队呢..哈哈.
决定是你了!

二话不说去瞧了一瞧那网站长什么样,顺带就顺手注册了帐号什么的.

[singlepic id=741 w=600 h=480 mode=watermark float=center]

登录进去之后直接提示了一个在线的翻译系统,强大啊.....进去之后默认是显示自己的项目,可以选择列表全部的ff扩展,直接搜索sqlite就能出现我们想要的sqlite-manager啦:

[singlepic id=746 w=600 h=480 mode=watermark float=center]

如上图所示啊.他有一个总述...但是最让人即欢喜又伤心的是,目前这个插件已经翻译成了我的语言:zh-CN......嗯.也少了不少的时间啦~
强制进去.发现真的全部都翻译好了(这还有不信的吗..额...)....给力啊~二话不说赶紧把这些翻译文件全部下载下来...哦耶~

[singlepic id=755 w=640 h=480 mode=watermark float=right]

(PS:如果进入一个扩展发现中文还没有完全汉化翻译,可以直接点进去不同的文件,系统会帮你进行关键翻译词的提取,然后在下面的文本框直接输入翻译就可以提交了~最后会通过一部分人的审阅就可以正式作为翻译资料呗他人使用啦~详见我之前的一篇日志,两者几乎完全一样.<a title="今天的小收获" href="http://59.64.139.125/archives/436" target="_blank">[点这里]</a>)
<h2 >第二部分 将中文翻译文件集成进入扩展中</h2>
好了.现在我们有了给力的中文翻译文件了,接下来就是将这些文件集成进安装的扩展当中...按照网上提供的说法,firefox的扩展默认都是安装在"c:\Documents and Settings\你的当前用户名\AppData\Roaming\Mozilla\Firefox\Profiles\yukhjqta.default（这个名可能有变化）\extensions"下面的..进去一看.确实有很多安装的扩展的文件夹的一堆东西...

[singlepic id=749 w=320 h=480 mode=watermark float=center]

大致的晃了一圈之后没有发现叫sqlite 的文件夹啊...囧.又谷歌了之后发现全部扩展的位置可以通过在ff地址栏输入"about:support"来展示,看了之后发现真的很奇怪.
sqlite-manager的竟然还是一个xpi的文件.貌似没解压?
真奇怪啊真奇怪.....但是不影响我们之后的添加就是了...(我怀疑firefox是在每次启动这个扩展的时候再现场加载到内存什么的...也没有想去深入研究他,等需要的时候再去看呗)

[singlepic id=750 w=600 h=480 mode=watermark float=center]

接着只好直接把这个xpi文件解压出来慢慢分析了....
很好,里面确实有且只有他官网所说的那几个语言文件夹.嗯既然这样的话我们就直接把下下来的中文语言文件放入新建的叫"zh-CN"的文件夹里面呗..然后直接在打开xpi的目录下面保存,直接保存更新一下这个xpi就ok了.....
很好.我以为这样就可以了..............
然后打非常高兴的屁颠屁颠的去开ff,再开sqlite-manager.额.为什么没变化?嗯?哪里出错了?我命名原样打包回去了的啊???发生了什么?嗯?

好吧..我确实是个ff扩展的小白.一直都是下下来直接用,从来没有去查阅过什么xpi的格式什么的...没办法.只能按照最老的方式,将整个xpi解压到一个文件夹里面,一个文件一个文件的去看内容,看看什么和语言设置有关了....
按照不知道从哪来的经验与直觉(喂!)首先打开的就是根目录下面的两个文件,第一个叫"install.rdf"的文件.搜了一下没有与locale相关的文字.嗯.hold住,不急,继续看下一个文件...

[singlepic id=744 w=600 h=480 mode=watermark float=center]

哈哈,又是直接就发现了很多关于语言的地方啊...不错不错...就是红色的两个框锁展示的.地方.瞬间明白了..原来还需要在这个文件把中文的地方加上去啊...嗯嗯,加上之后的文件内容就变成了:

[singlepic id=756 w=600 h=480 mode=watermark float=center]

嗯,之后又大概看了看文件夹下面的其他文件,也没发现什么其他需要仔细注意的地方了.嗯~保存~打包~测试kaishi~

嗯.为什么还是不行?嗯?我把整个包里面的全部涉及到语言的地方都改了啊!!!

<strong><span >好吧.来点狠的..删了之前的安装重装这个xpi!</span></strong>

嗯....经过不屑的努力.终于成功了!.来,给爷笑一个~嗯嗯.

[singlepic id=754 w=600 h=240 mode=watermark float=center]

<span >----------------------------<del>分隔线</del>------------------------------------------</span>

后来经过测试:直接强制修改默认的locale也能达到一样的效果,直接截个图好了~

如图:

[singlepic id=753 w=600 h=480 mode=watermark float=center]

其他的部分都不用变
<h2 ><span >第三部分 涉及的文件下载</span></h2>
<ul>
	<li>首先呢,肯定是你必须得有firefox:点这里就可以下载最新版啦~:
<a href="http://www.mozilla.org/firefox?WT.mc_id=aff_en17&amp;WT.mc_ev=click"><img class="aligncenter" alt="Firefox Download Button" src="http://www.mozilla.org/contribute/buttons/468x60arrow_r.png" /></a></li>
	<li>接着呢.是sqlite-manager的最新版啦~:<a href="http://code.google.com/p/sqlite-manager/downloads/list" target="_blank">http://code.google.com/p/sqlite-manager/downloads/list</a></li>
	<li>嗯,然后是中文的最新翻译文件啦~:<a href="http://www.babelzilla.org" target="_blank">www.babelzilla.org</a> 在里面怎么弄就自己摸索一下了吧~
也可以下载我这里提供的文件:(20110915版):<a href="http://blog.zzjin.net/wp-content/uploads/2011/09/zh-CN.zip">zh-CN</a></li>
	<li>嗯.然后虽然说是应该提供修改之后的"chrome.manifest"文件...但是两种修改方法实在是太简单了...直接看截图照着改就行啦~</li>
	<li>嘛.还是提供一个我修改好了之后的xpi文件作为本篇教程(伪....(你也有自知之明啊...))的结束吧~
[wpfilebase tag=file id=27]</li>
</ul>
<h3>最后的最后,简单的说一下.....</h3>
其实我觉得这个日志对很多人来说根本就是不值得一看的纯小八卦文章,而且还写得又臭又长..里面还混杂了很多的自我吐槽和neta什么的...唉~不过其实我写这些东西的目的也很简单.
<ol>
	<li>经验的保存.不管是做了什么,都是对自己工作的一个肯定,这几个小时弄这个出来成果的非常棒的成就感享受~</li>
	<li>一定的教学,虽然写得比较乱,但是从截图到排版我都是花了很大的心思来处理的..为了能很直观方便的展现给想自己弄弄firefox扩展的同学,那些想折腾的人.应该也能期待一定的教学作用吧~</li>
	<li>
<h4>其实也是最重要的一点,我想从这个文章当中教会一些人,一些愿意学习的人一些方法.
对于firefox的插件的机制这个东西,我在今天之前都是完全没有一点了解的.但是本着一颗<del>奋进专研</del>折腾再折腾的心去查资料,
尝试,测试,向论坛寻求帮助等等的方法来达到目的,一次不成功,自己分析肯能的原因,再重新进行超找与测试等等,
而不是上来就张口要最后的结果,要别人帮你做好一切,这样也许到最后,到几年之后也还是原地踏步....
这也许是一个it人员应有的基础.我是如此希望着的...</h4>
</li>
	<li>嗯,希望大牛们别喷我太厉害就好...</li>
</ol>