---
layout: post
title:  "which whereis locate find 用法"
date:   2016-4-14 10:51:19 
categories: jekyll update
---

我们经常在linux要查找某个文件，但不知道放在哪里了，可以使用下面的一些命令来搜索。这些是从网上找到的资料，因为有时很长时间不会用到，当要用的时候经常弄混了，所以放到这里方便使用。 

	which	在PATH变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。
	whereis	whereis命令只能用于程序名的搜索，而且只搜索二进制文件（参数-b）、man说明文件（参数-m）和源代码文件（参数-s）。如果省略参数，则返回所有信息。
	locate	配合数据库查看文件位置。
	find	实际搜寻硬盘查询文件名称。

###1、which ###

语法： 

	[root@redhat ~]# which 可执行文件名称 

例如： 

	[root@redhat ~]# which passwd 
	/usr/bin/passwd 

which命令的作用是，在PATH变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。也就是说，使用which命令，就可以看到某个系统命令是否存在，以及执行的到底是哪一个位置的命令。
which指令会在环境变量$PATH设置的目录里查找符合条件的文件，所以基本的功能是寻找可执行文件

###2、whereis ###

whereis指令查找符合条件的文件。这些文件的烈性应属于原始代码，二进制文件，或是帮助文件.
whereis命令只能用于程序名的搜索，而且只搜索二进制文件（参数-b）、man说明文件（参数-m）和源代码文件（参数-s）。如果省略参数，则返回所有信息。

语法： 

	[root@redhat ~]# whereis [-bmsu] 文件或者目录名称 

参数说明： 

	-b ： 只找二进制文件 
	-m ： 只找在说明文件manual路径下的文件 
	-s ： 只找source源文件 
	-u ： 没有说明文档的文件 

例如： 

	[root@redhat ~]# whereis passwd 
	passwd: /usr/bin/passwd /etc/passwd /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz 

将和passwd文件相关的文件都查找出来 

	[root@redhat ~]# whereis -b passwd 
	passwd: /usr/bin/passwd /etc/passwd 

只将二进制文件查找出来 

和find相比，locate查找的速度非常快，这是因为linux系统会将系统内的所有文件都记录在一个数据库文件中，当使用locate时，会从数据库中查找数据，而不是像find命令那样，通过遍历硬盘来查找，效率自然会很高。 

但是该数据库文件并不是实时更新，默认情况下时一星期更新一次，因此，我们在用locate 查找文件时，有时会找到已经被删除的数据，或者刚刚建立文件，却无法查找到，原因就是因为数据库文件没有被更新(使用updatedb命令进行更新)。

###3、 locate ###

语法： 

	[root@redhat ~]# locate 文件或者目录名称

例 如：

查看locate版本信息-Ｖ

	root@pts/0 # locate -V
	mlocate 0.22.2
	Copyright (C) 2007 Red Hat, Inc. All rights reserved.
	This software is distributed under the GPL v.2.
	
	This program is provided with NO WARRANTY, to the extent permitted by law.

查看locate帮助信息

	root@pts/0 # locate -h
	Usage: locate [OPTION]... [PATTERN]...
	Search for entries in a mlocate database.
	
	  -b, --basename         匹配唯一的路径名称的基本文件名
	  -c, --count            只显示找到条目的号码
	  -d, --database DBPATH  DBPATH 用 DBPATH 替代默认的数据库(/var/lib/mlocate/mlocate.db) 
	  -e, --existing         只显示当前存在的文件条目
	  -L, --follow           当文件存在时跟随蔓延的符号链接 (默认)
	  -h, --help             显示本帮助
	  -i, --ignore-case      匹配模式时忽略大小写区别
	  -l, --limit, -n LIMIT  限制为 LIMIT项目的输出 (或 计数)
	  -m, --mmap             忽略向后兼容性
	  -P, --nofollow, -H     当检查文件时不跟随蔓延的符号链接
	  -0, --null             输出时以 NUL 分隔项目
	  -S, --statistics       不搜索项目,显示有关每个已用数据库的统计信息
	  -q, --quiet            不报告关于读取数据库的错误消息
	  -r, --regexp REGEXP    搜索基本正则表达式 REGEXP 来代替模式
	      --regex            模式是扩展正则表达式
	  -s, --stdio            忽略向后兼容性
	  -V, --version          显示版本信息
	  -w, --wholename        匹配完整路径名 (默认)  
	
	将 bug 报告给 mitr@redhat.com. 

###4、 find ###

语法： 

	[root@redhat ~]# find 路径 参数

示例：

	[root@redhat ~]# find / -name zgz 
	/home/zgz 
	/home/zgz/zgz 
	/home/weblogic/bea/user_projects/domains/zgz 
	/home/oracle/product/10g/cfgtoollogs/dbca/zgz 
	/home/oracle/product/10g/cfgtoollogs/emca/zgz 
	/home/oracle/oradata/zgz

+++++++++++++++++++++++++++++++++++++++++++++++++++++

当我们用locate无法查找到我们需要的文件时，可以使用find，但是find是在硬盘上遍历查找，因此非常消耗硬盘的资源，而且效率也非常低，因此建议大家优先使用whereis和locate,可以在使用locate之前，先使用updatedb命令，手动更新数据库。

	locate 是在数据库里查找，数据库大至每天更新一次。 
	whereis 可以找到可执行命令和man page 
	find 就是根据条件查找文件。 
	which 可以找到可执行文件和别名(alias)