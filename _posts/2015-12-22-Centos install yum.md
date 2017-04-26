---
layout: post
title:  "Centos install yum"
date:   2015-12-22 星期二  11:08:34    
categories: jekyll update
---

	rpm -Uhv http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm

	rpm -Uhv http://rpms.famillecollet.com/enterprise/remi-release-6.rpm

	yum makecache

	rpm -Uhv http://packages.sw.be/rpmforge-release/rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm

下载centos的yum

    Centos 6 
	wget http://mirrors.163.com/.help/CentOS6-Base-163.repo

	Centos 7
	wget http://mirrors.163.com/.help/CentOS7-Base-163.repo

    Centos 5
	wget http://mirrors.163.com/.help/CentOS5-Base-163.repo