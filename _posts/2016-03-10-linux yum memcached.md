---
layout: post
title:  "linux yum memcached"
date:   2016-3-10 星期四  20:40:40 
categories: jekyll update
---

###安装###

	root@pts/0 # yum -y install memcached

###启动服务###

	root@pts/0 # /etc/init.d/memcached start

###配置文件###

	root@pts/0 # vim /etc/sysconfig/memcached

	PORT="11211"
	USER="memcached"
	MAXCONN="1024"
	CACHESIZE="64"
	OPTIONS=""

###安装目录：###