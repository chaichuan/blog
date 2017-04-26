---
layout: post
title:  "centos 7 安装docker"
date:   2016-8-24 22:59:56 
categories: jekyll update
---

###安装docker###

Docker 软件包已经包括在默认的 CentOS-Extras 软件源里。因此想要安装 docker，只需要运行下面的 yum 命令：

	[root@localhost ~]# yum install docker

###启动 Docker 服务###

安装完成后，使用下面的命令来启动 docker 服务，并将其设置为开机启动：

	[root@localhost ~]# service docker start
	[root@localhost ~]# chkconfig docker on

（译注：此处采用了旧式的 sysv 语法，如采用CentOS 7中支持的新式 systemd 语法，如下：

	[root@localhost ~]# systemctl  start docker.service
	[root@localhost ~]# systemctl  enable docker.service

）

###下载官方的 CentOS 镜像到本地###

 （译注：由于 Docker 被墙 :-< ，所以请使用 http://docker.cn  的镜像，感谢 @马全一 的镜像。 ）

	[root@localhost ~]# docker pull centos

###确认 CentOS 镜像已经被获取：###

	[root@localhost ~]# docker images centos
	REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
	docker.io/centos    latest              970633036444        3 weeks ago         196.7 MB

###运行一个 Docker 容器：###

	[root@localhost ~]# docker run -i -t centos /bin/bash
	[root@dbf66395436d /]#

我们可以看到，CentOS 容器已经被启动，并且我们得到了 bash 提示符。在 docker 命令中我们使用了 “-i 捕获标准输入输出”和 “-t 分配一个终端或控制台”选项。若要断开与容器的连接，输入 exit。

	[root@126c2c096728 /]# cat /etc/redhat-release 
	CentOS Linux release 7.2.1511 (Core)  
	[root@126c2c096728 /]# exit

我们还可以搜索基于 Fedora 和 Ubuntu 操作系统的容器。

	[root@localhost ~]# docker search ubuntu
	[root@localhost ~]# docker search fedora