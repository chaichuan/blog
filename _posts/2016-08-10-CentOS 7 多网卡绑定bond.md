---
layout: post
title:  "CentOS 7 多网卡绑定bond"
date:   2016-8-10 19:35:49 
categories: jekyll update
---


Centos/REHL 7相对之前的版本有了很大改动，有很多之前版本的方法都已经不适用了。网络方面改动也非常大，通过之前脚本进行网卡改动一直不行。有的文章说通过通过 sysfs 接口配置 Bonding的方法配置，不过俺做一直报错

	# echo +eno33554992 > /sys/class/net/bond0/bonding/slaves 

	-bash: echo: write error: Operation not permitted，原因尚为查出。

根据官方文档Red_Hat_Enterprise_Linux-7-Networking_Guide-en-US用nmcli做起来还是相当容易的。下面把俺的步骤贴下。

1、查看目前网卡的名称和状态。

	#nmcli device status 
	DEVICE       TYPE      STATE         CONNECTION 
	eno16777736  ethernet  connected     eth1       
	eno33554992  ethernet  disconnected  --         
	eno50332216  ethernet  disconnected  --         
	lo           loopback  unmanaged     --

2、配置网卡bond0和模式主备

	nmcli connection add type bond con-name bond0 ifname bond0 mode actiive-backup 

	Connection 'bond0' (37a78146-9489-4686-bc62-227cb3908292) successfully added

3、将物理网卡绑定到bond0

	# nmcli connection add type bond-slave ifname eno50332216 master bond0
	
	Connection 'bond-slave-eno50332216' (3f07e767-24ea-4e7e-a9f7-61efa9aa0664) successfully added.
	# nmcli connection add type bond-slave ifname eno33554992 master bond0
	
	Connection 'bond-slave-eno33554992' (8149c24f-4926-4531-844f-36a526e1baec) successfully added.

4、启用绑定的物理网卡

	# nmcli connection up bond-slave-eno50332216
	Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/4)
	# nmcli connection up bond-slave-eno33554992 
	
	Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/5)

5、启用bond网卡

	#nmcli connection up bond0
	
	Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/6)

现在网卡已经绑好启用

	# ifconfig -a
	bond0: flags=5187<UP,BROADCAST,RUNNING,MASTER,MULTICAST>  mtu 1500
	        inet6 fe80::20c:29ff:febe:5d85  prefixlen 64  scopeid 0x20<link>
	        ether 00:0c:29:be:5d:85  txqueuelen 0  (Ethernet)
	        RX packets 38  bytes 4436 (4.3 KiB)
	        RX errors 0  dropped 11  overruns 0  frame 0
	        TX packets 60  bytes 9324 (9.1 KiB)
	        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
	
	eno16777736: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
	        inet 192.168.47.71  netmask 255.255.255.0  broadcast 192.168.47.255
	        inet6 fe80::20c:29ff:febe:5d7b  prefixlen 64  scopeid 0x20<link>
	        ether 00:0c:29:be:5d:7b  txqueuelen 1000  (Ethernet)
	        RX packets 876  bytes 77457 (75.6 KiB)
	        RX errors 0  dropped 0  overruns 0  frame 0
	        TX packets 632  bytes 73445 (71.7 KiB)
	        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
	
	eno33554992: flags=6211<UP,BROADCAST,RUNNING,SLAVE,MULTICAST>  mtu 1500
	        ether 00:0c:29:be:5d:85  txqueuelen 1000  (Ethernet)
	        RX packets 23  bytes 2898 (2.8 KiB)
	        RX errors 0  dropped 10  overruns 0  frame 0
	        TX packets 56  bytes 8940 (8.7 KiB)
	        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
	
	eno50332216: flags=6211<UP,BROADCAST,RUNNING,SLAVE,MULTICAST>  mtu 1500
	        ether 00:0c:29:be:5d:85  txqueuelen 1000  (Ethernet)
	        RX packets 15  bytes 1538 (1.5 KiB)
	        RX errors 0  dropped 1  overruns 0  frame 0
	        TX packets 4  bytes 384 (384.0 B)
	        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
	
	lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
	        inet 127.0.0.1  netmask 255.0.0.0
	        inet6 ::1  prefixlen 128  scopeid 0x10<host>
	        loop  txqueuelen 0  (Local Loopback)
	        RX packets 17  bytes 1640 (1.6 KiB)
	        RX errors 0  dropped 0  overruns 0  frame 0
	        TX packets 17  bytes 1640 (1.6 KiB)
	        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

地址之类的可以后加

	ip addr add 192.168.200.200/24 dev bond0

也可以通过nmcli添加，大家查下手册即可

	Usage: nmcli connection { COMMAND | help }
	
	COMMAND := { show | up | down | add | modify | edit | delete | reload | load }
	
	  show [--active] [[id | uuid | path | apath] <ID>] ...
	
	  up [[id | uuid | path] <ID>] [ifname <ifname>] [ap <BSSID>]
	
	  down [id | uuid | path | apath] <ID>
	
	  add COMMON_OPTIONS TYPE_SPECIFIC_OPTIONS IP_OPTIONS
	
	  modify [--temporary] [id | uuid | path] <ID> ([+|-]<setting>.<property> <value>)+
	
	  edit [id | uuid | path] <ID>
	  edit [type <new_con_type>] [con-name <new_con_name>]
	
	  delete [id | uuid | path] <ID>
	
	  reload
	
	  load <filename> [ <filename>... ]