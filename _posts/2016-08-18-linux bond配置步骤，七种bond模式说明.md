---
layout: post
title:  "linux bond配置步骤，七种bond模式说明"
date:   2016-8-18 11:24:29 
categories: jekyll update
---

##一、网卡绑定：##

###第一步：创建一个ifcfg-bondX###

	# vi /etc/sysconfig/network-scripts/ifcfg-bond0
	DEVICE=bond0
	BONDING_OPTS="mode=0 miimon=100"
	BOOTPROTO=none
	ONBOOT=yes
	IPADDR=192.168.0.180
	NETMASK=255.255.255.0
	NETWORK=192.168.0.0
	USERCTL=no

BONDING_OPTS="mode=0 miimon=100" ，mode有多种模式实现不同的功能


###第二步：修改/etc/sysconfig/network-scripts /ifcfg-ethX###

	# vi /etc/sysconfig/network-scripts /ifcfg-eth0
	DEVICE=eth0
	BOOTPROTO=none
	ONBOOT=yes
	MASTER=bond0
	SLAVE=yes
	USERCTL=no
	# vi /etc/sysconfig/network-scripts /ifcfg-eth1
	DEVICE=eth1
	BOOTPROTO=none
	ONBOOT=yes
	MASTER=bond0
	SLAVE=yes
	USERCTL=no


###第三步：配置/etc/modprobe.conf，添加alias bond0 bonding###

	alias scsi_hostadapter mptbase
	alias scsi_hostadapter1 mptspi
	alias scsi_hostadapter2 ata_piix
	alias scsi_hostadapter3 ahci
	alias net-pf-10 off
	alias ipv6 off
	options ipv6 disable=1
	alias eth0 b0                后面分组的名字，eth0 和 eth3 对应的是bond0
	alias eth1 b1
	alias eth2 e1000
	alias eth3 b0
	alias eth4 b1
	alias bond0 bonding
	alias bond1 bonding
	options bond0 miimon=100 mode=1 primary=eth0
	options bond1 miimon=100 mode=1 primary=eth1
	#options bonding max_bonds=2 miimon=200 mode=1


###第四步：重启网络服务###

	#service network restart


通过查看/proc/net/bonding/bond0，查看当前是用什么mode，如果是主备的话，当前是哪个网卡工作。

	cat /proc/net/bonding/bond0 
	Ethernet Channel Bonding Driver: v3.4.0-1 (October 7, 2008)
	
	Bonding Mode: fault-tolerance (active-backup)
	Primary Slave: eth0 (primary_reselect always)
	Currently Active Slave: eth0
	MII Status: up
	MII Polling Interval (ms): 100
	Up Delay (ms): 0
	Down Delay (ms): 0
	
	Slave Interface: eth0
	MII Status: up
	Speed: 1000 Mbps
	Duplex: full
	Link Failure Count: 0
	Permanent HW addr: 00:0c:29:c8:7f:34
	
	Slave Interface: eth3
	MII Status: up
	Speed: 1000 Mbps
	Duplex: full
	Link Failure Count: 0
	Permanent HW addr: 00:0c:29:c8:7f:52
	
---

	cat /proc/net/bonding/bond1
	Ethernet Channel Bonding Driver: v3.4.0-1 (October 7, 2008)
	
	Bonding Mode: fault-tolerance (active-backup)
	Primary Slave: None
	Currently Active Slave: eth1
	MII Status: up
	MII Polling Interval (ms): 100
	Up Delay (ms): 0
	Down Delay (ms): 0
	
	Slave Interface: eth1
	MII Status: up
	Speed: 1000 Mbps
	Duplex: full
	Link Failure Count: 0
	Permanent HW addr: 00:0c:29:c8:7f:3e
	
	Slave Interface: eth4
	MII Status: up
	Speed: 1000 Mbps
	Duplex: full
	Link Failure Count: 0
	Permanent HW addr: 00:0c:29:c8:7f:5c


##二、七种bond模式说明：##

###第一种模式：mod=0 ，即：(balance-rr) Round-robin policy（平衡抡循环策略）###

特点：传输数据包顺序是依次传输（即：第1个包走eth0，下一个包就走eth1….一直循环下去，直到最后一个传输完毕），此模式提供负载平衡和容错能力；但是我们知道如果一个连接或者会话的数据包从不同的接口发出的话，中途再经过不同的链路，在客户端很有可能会出现数据包无序到达的问题，而无序到达的数据包需要重新要求被发送，这样网络的吞吐量就会下降

###第二种模式：mod=1，即： (active-backup) Active-backup policy（主-备份策略）###

特点：只有一个设备处于活动状态，当一个宕掉另一个马上由备份转换为主设备。mac地址是外部可见得，从外面看来，bond的MAC地址是唯一的，以避免switch(交换机)发生混乱。此模式只提供了容错能力；由此可见此算法的优点是可以提供高网络连接的可用性，但是它的资源利用率较低，只有一个接口处于工作状态，在有 N 个网络接口的情况下，资源利用率为1/N

###第三种模式：mod=2，即：(balance-xor) XOR policy（平衡策略）###

特点：基于指定的传输HASH策略传输数据包。缺省的策略是：(源MAC地址 XOR 目标MAC地址) % slave数量。其他的传输策略可以通过xmit_hash_policy选项指定，此模式提供负载平衡和容错能力

###第四种模式：mod=3，即：broadcast（广播策略）###

特点：在每个slave接口上传输每个数据包，此模式提供了容错能力

###第五种模式：mod=4，即：(802.3ad) IEEE 802.3adDynamic link aggregation（IEEE 802.3ad 动态链接聚合）###

特点：创建一个聚合组，它们共享同样的速率和双工设定。根据802.3ad规范将多个slave工作在同一个激活的聚合体下。

外出流量的slave选举是基于传输hash策略，该策略可以通过xmit_hash_policy选项从缺省的XOR策略改变到其他策略。需要注意的是，并不是所有的传输策略都是802.3ad适应的，尤其考虑到在802.3ad标准43.2.4章节提及的包乱序问题。不同的实现可能会有不同的适应性。

必要条件：

条件1：ethtool支持获取每个slave的速率和双工设定

条件2：switch(交换机)支持IEEE 802.3ad Dynamic link aggregation

条件3：大多数switch(交换机)需要经过特定配置才能支持802.3ad模式

###第六种模式：mod=5，即：(balance-tlb) Adaptive transmit load balancing（适配器传输负载均衡）###

特点：不需要任何特别的switch(交换机)支持的通道bonding。在每个slave上根据当前的负载（根据速度计算）分配外出流量。如果正在接受数据的slave出故障了，另一个slave接管失败的slave的MAC地址。

该模式的必要条件：ethtool支持获取每个slave的速率

###第七种模式：mod=6，即：(balance-alb) Adaptive load balancing（适配器适应性负载均衡）###

特点：该模式包含了balance-tlb模式，同时加上针对IPV4流量的接收负载均衡(receive load balance, rlb)，而且不需要任何switch(交换机)的支持。接收负载均衡是通过ARP协商实现的。bonding驱动截获本机发送的ARP应答，并把源硬件地址改写为bond中某个slave的唯一硬件地址，从而使得不同的对端使用不同的硬件地址进行通信。