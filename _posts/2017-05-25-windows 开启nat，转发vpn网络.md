---
layout: post
title:  "windows 开启nat，转发vpn网络"
date:   2017-5-25 15:32:42 
categories: jekyll update
---

情景：

公司内代码服务器需要连接IDC的网路，IDC开启了cisco ipsec vpn，由于linux 系统部署vpn client 比较麻烦，准备在windows上安装客户端，拨好vpn，开启nat转发，然后代码服务器做静态路由，通过windows 的vpn 访问IDC

![](http://7xppz2.com1.z0.glb.clouddn.com/95.png)

先在windows拨好vpn

linux写静态路由：

	route add -net 192.168.2.0/24 gw 192.168.1.20

windows开启nat功能：

本地网卡  选择  “专用接口连接到专用网路”

![](http://7xppz2.com1.z0.glb.clouddn.com/93.png)

cisco网卡  选择  “公用接口连接到Internet” ，并勾选 “在此接口上启用NAT”

![](http://7xppz2.com1.z0.glb.clouddn.com/94.png)

并且注意windows server的防火墙