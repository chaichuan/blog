---
layout: post
title:  "Zabbix agent 在windows上安装部署"
date:   2016-5-10 18:27:41 
categories: jekyll update
---

1、  下载与解压

	地址: http://www.zabbix.com/downloads/3.0/zabbix_agents_3.0.0.win.zip

	解压zabbix_agents_3.0.0.win.zip

	conf目录存放是agent配置文件 bin文件存放windows下32位和64位安装程序

2、  配置与安装

配置zabbix agent相关配置。

	找到conf下的配置文件 zabbix_agentd.win.conf ，修改LogFile、Server、Hostname这三个参数。具体配置如下：

	LogFile=c:\zabbix_agentd.log
	
	Server=192.168.68.220
	
	Hostname=lanserver
	
	ServerActive=192.168.68.220 #zabbix server地址
	
	其中logfile是zabbix日志存放地址。Server 是zabbix服务端ip地址。Hostname是本机机器名。

安装agent

	在windows控制台下执行以下命令：

	E:\zabbix\bin\win32\zabbix_agentd.exe  -c E:\zabbix\conf\zabbix_agentd.win.conf –i 

启动agent客户端

	启动命令如下：

	E:\zabbix\bin\win32\zabbix_agentd.exe  -c E:\zabbix\conf\zabbix_agentd.win.conf –s

 

启动的程序将以控制台的形式开启。查看启动的日志：

	  8684:20160510:183346.853 Starting Zabbix Agent [RTX server]. Zabbix 3.0.0 (revision 58455).
	  8684:20160510:183346.853 **** Enabled features ****
	  8684:20160510:183346.853 IPv6 support:          YES
	  8684:20160510:183346.853 TLS support:            NO
	  8684:20160510:183346.853 **************************
	  8684:20160510:183346.853 using configuration file: D:\zabbix_agents_3.0.0.win\conf\zabbix_agentd.win.conf
	  8684:20160510:183346.853 agent #0 started [main process]
	  2412:20160510:183346.853 agent #1 started [collector]
	  7104:20160510:183346.853 agent #2 started [listener #1]
	  9184:20160510:183346.853 agent #3 started [listener #2]
	  9792:20160510:183346.853 agent #5 started [active checks #1]
	  7556:20160510:183346.853 agent #4 started [listener #3]