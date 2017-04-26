---
layout: post
title:  "linux shell显示 中英文修改"
date:   2016-1-10 星期日  21:17:21
categories: jekyll update
---


###1、查看配置文件###

	root@pts/0 # cat /etc/sysconfig/i18n  
	LANG="en_US.UTF-8"

以上说明系统显示英文

###2、如果想修改为显示中文，修改 /etc/sysconfig/i18n 配置文件###

	root@pts/0 # vim /etc/sysconfig/i18n
	LANG="zh_CN.UTF-8"
	：wq  --> 保存退出
	root@pts/0 # source /etc/sysconfig/i18n
