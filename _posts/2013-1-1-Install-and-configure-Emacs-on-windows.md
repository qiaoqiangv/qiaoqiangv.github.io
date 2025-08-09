---
layout: post
title: windows下Emacs的安装与配置 
---

 
emacs windows lisp linux path文档

 
最近在学习windows下的Emacs，遇到不少问题，比如什么home目录啦，.emacs配置文件啦，.el文件啦，通过几天的反复琢磨，终于有所感悟。我想不仅是我，很多人都遇到过这些问题，现在就总结如下，以供有需要的朋友参考。
1、下载
到这个网址可以下载到Emacs的windows版本：http://ftp.gnu.org/pub/gnu/emacs/windows/
只需要一个压缩文档，如emacs-22.3-bin-i386.zip
 
2、安装
在D盘根目录下新建一个文件夹，取名Emacs22.2（也可以是其他路径，随个人喜好而定），将emacs-22.2-bin-i386.zip里的文件解压到这个目录下，这样在d:/Emacs22.2/下就有bin, tec, info, leim, lisp, site-lisp等目录。
 
双击bin文件夹里的addpm.exe进行安装，安装后将在开始菜单生成Gnu Emacs/Emacs链接，点击这个链接便可启动Emacs。也可以双击bin文件夹里的runemacs.exe启动。注意到bin目录里还有个文件是emacs.exe，双击它也可以启动，但是会出现一个控制台窗口
 
3、修改注册表
打开注册表，找到HKEY_LOCAL_MACHINE/SOFTWARE/GNU/Emacs（如果没有则手动添加项），在此项下添加字符串值，名称为HOME，值为D:/Emacs22.2。这样做的目的是让D:/Emacs22.2成为Emacs的home路径（传说中的home path，以后你将会经常看到“home目录”、“home directory”等等）。
 
4、创建.emacs.d目录和.emacs文件
相信.emacs.d目录和.emacs文件是困扰大家很久的问题了，其实有个简单的办法可以解决此问题。启动emacs，用鼠标点击Options菜单，随便点击一两个选项，比如点击一下Active Region Highlighting，然后点击Save Options。先不要担心你会破坏了什么东西，这样做的目的是让emacs自动创建.emacs.d目录以及.emacs文件！观察你的Emacs窗口最后一行，是否显示“Wrote d:/Emacs22.2/.emacs”？如果是的话就对了，当你选择Save Options的时候，Emacs会在home路径下产生.emacs文件，并把配置信息写进这个文件。现在看看你的d:/Emacs22.2/目录下是否产生了这两个东西？
 
5、加载.el文件
lisp目录下存放着lisp源文件（*.el）和已编译的lisp文件（*.elc），以后你也可以将自己的.el文件放在这个目录下，然后还要在.emacs文件插入相关语句。比如你有一个文件叫做abcd.el，将它复制到lisp目录下，然后打开.emacs文件插入一句(require 'abcd)就可以了（包括圆括号，不需要扩展名.el）。
 
如果你不喜欢lisp文件夹，也可以自己新建一个，比如在home目录下建一个文件夹叫做xyz，然后把abcd.el放在xyz目录下，在.emacs文件插入以下两句：
(setq load-path (cons "~/xyz" load-path))
(require 'abcd)
第一句告诉emacs先加载你的xyz目录，第二句再加载abcd.el。注意“~/”是linux系统的用法，表示home目录。
如果你和我一样在学习《Sams Teach Yourself Emacs in 24 Hours》这本书的话，我想你一定需要sams-lib.el这个文件！可以到这个网址下载：
http://www.cs.virginia.edu/~wh5a/personal/Emacs/
找到sams-lib.el之后右键点击“目标另存为”就可以了！

 
最后，在下有一事不解，除了lisp还有一个site-lisp目录，我把sams-lib.el分别放在这两个目录下，发现效果是一样的，不知道这两个目录有何不同之处？
