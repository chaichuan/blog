---
layout: post
title:  "shell脚本执行冲突事件-ssh&while"
date:   2016-12-22 18:03:25 
categories: jekyll update
---

今天发现一个问题：
 
先看下脚本内容：

	root@pts/0 # cat !$
	cat /tmp/test_nginx
	ls *.txt | tr ' ' '\n' | while read line
	do 
	echo $line
	if [ 'yes' == 'yes' ]
	then
	ssh  192.168.109.10 "echo 'yes'"
	else
	ssh 192.168.109.10 "echo 'no'"
	fi
	done
 
上面脚本很简单，读取当前目录的所有以txt结尾的文件（实际上我只是取得循环次数），但我们
运行脚本会发现脚本只循环一次就退出了。效果如下：
 
	root@pts/0 # bash -x /tmp/test_nginx       
	+ tr ' ' '\n'
	+ ls 1.txt 2.txt ip.txt png.txt       
	+ read line
	+ echo 1.txt
	1.txt
	+ '[' yes == yes ']'
	+ ssh 192.168.9.10 'echo '\''yes'\'''
	yes
	+ read line
	 
 
此时恭喜你，你如愿接触到ssh 和while 冲突事件了!
 
办法始终比较问题多。
解决办法很简单：
 
方法一:
 
	  ssh  192.168.109.10 "echo 'yes'"  
	  改为  ssh  192.168.109.10 "echo 'yes'"  < /dev/null
   
方法二：

	  ssh  192.168.109.10 "echo 'yes'"  
	  改为  ssh  -n 192.168.109.10 "echo 'yes'" 
   
   
产生的原因：

ssh 命令将while未执行的循环给读取了，下面内容可以帮助你理解。
 
	root@pts/0 # bash -x /tmp/test_nginx 
	+ ls 1.txt 2.txt ip.txt png.txt
	+ tr ' ' '\n'
	+ read line
	+ echo 1.txt
	1.txt
	+ '[' yes == yes ']'
	+ ssh 192.168.9.10 cat     //我们将命令改成cat，就会发现了。
	2.txt
	ip.txt
	png.txt
	+ read line