---
layout: post
title:  "Linux下Vi配置文件.vimrc文件"
date:   2016-12-22 17:55:09 
categories: jekyll update
---


配置 vim 的方法是在用户主目录下建立个.vimrc文件，我一般使用root帐户，所以就在/root/下建立一个.vimrc文件：vi /root/.vimrc，以下是我的配置，直接复制粘贴进去即可。

用户可以根据需求添加功能， 关于 set 的使用方法可以在 vim 的命令状态下输入：set help 获取帮助。

××××××××××××××××××××

	"语法高亮
	syntax on
	"显示行号
	set nu
	"修改默认注释颜色
	hi Comment ctermfg=DarkCyan
	"允许退格键删除
	set backspace=2
	"启用鼠标
	set mouse=a
	set selection=exclusive
	set selectmode=mouse,key
	"侦测文件类型
	filetype on
	"载入文件类型插件
	filetype plugin on
	"为特定文件类型载入相关缩进文件
	filetype indent on
	"设置编码自动识别, 中文引号显示
	set fileencodings=utf-8,gbk
	set encoding=euc-cn
	set ambiwidth=double
	"设置高亮搜索
	set hlsearch
	"在搜索时，输入的词句的逐字符高亮
	set incsearch
	"按C语言格式缩进
	set cindent
	"设置Tab长度为4格
	set tabstop=4
	"设置自动缩进长度为4格
	set shiftwidth=4
	"继承前一行的缩进方式，特别适用于多行注释
	set autoindent
	"显示括号匹配
	set showmatch
	"括号匹配显示时间为1(单位是十分之一秒)
	set matchtime=1
	"增强模式中的命令行自动完成操作
	set wildmenu
	"不要生成swap文件，当buffer被丢弃的时候隐藏它
	setlocal noswapfile
	set bufhidden=hide

这次装上debian后，远程ssh登陆后，vim出现了两个问题：

1、在~/.vimrc文件中设置syntax on后，运行vim，显示如下错误：

	Error detected while processing /home/administrator/.vimrc:
	line    5:
	E319: Sorry, the command is not available in this version: syntax on
	Press ENTER or type command to continue

这个问题是由于debian默认的vim版本的问题，运行命令apt-get install vim后会安装其他的软件包，安装完成后，就不再有这个问题了。

2、虽然不再报1中的错误，但仍然不显示颜色，而是使用下划线代替颜色

这个问题是由变量TERM的值不合适导致的，运行命令echo $TERM显示：vt100

在~/.bashrc中设置$TERM为：xterm-color，如下所示：

	export TERM=xterm-color

然后运行命令： source .bashrc后即可显示颜色。

另外，我想介绍一下vim的配置文件： /etc/vim/vimrc和~/.vimrc

1、/etc/vim/vimrc是全局的配置文件，修改这个配置文件将改变所有用户的vim配置。
这个文件还在目录/usr/share/vim目录下创建了链接文件，如下所示：

	root@pts/0 # ll
	total 16
	drwxr-xr-x 4 root root 4096 2009-04-26 19:13 addons
	drwxr-xr-x 2 root root 4096 2009-04-04 23:01 doc
	drwxr-xr-x 2 root root 4096 2009-04-26 19:13 registry
	drwxr-xr-x 17 root root 4096 2009-04-26 19:49 vim70
	lrwxrwxrwx 1 root root    5 2009-04-04 18:59 vimcurrent -> vim70
	lrwxrwxrwx 1 root root    8 2009-04-04 18:59 vimfiles -> /etc/vim
	lrwxrwxrwx 1 root root   14 2009-04-04 23:01 vimrc -> /etc/vim/vimrc
	lrwxrwxrwx 1 root root   19 2009-04-04 23:01 vimrc.tiny -> /etc/vim/vimrc.tiny
	

默认情况下，debian在这个配置文件中将语法颜色显示关掉了。某个用户要是需要语法颜色显示，可以在自己的主目录下建立文件.vimrc，在这个文件中修改配置，这样只会影响本用户的显示，这种方式更符合linux的精神。

2、~/.vimrc是用户自己的vim配置文件，在这个配置文件中设置的配置只影响该用户自己。