---
layout: post
title:  "linux 网卡多vlan 与 添加策略路由"
date:   2016-9-21 19:02:32 
categories: jekyll update
---

###本机配置了bond，mode 1

	root@pts/1 # cat ifcfg-bond0
	DEVICE=bond0
	BOOTPROTO=static
	ONBOOT=yes
	USERCTL=no
	BONDING_OPTS="mode=1 miimon=50" 

	root@pts/1 # cat ifcfg-eth0 
	DEVICE=eth0
	BOOTPROTO=none
	ONBOOT=yes
	MASTER=bond0 
	SLAVE=yes
	USERCTL=no

	root@pts/1 # cat ifcfg-eth1
	DEVICE=eth1
	BOOTPROTO=none
	ONBOOT=yes
	MASTER=bond0 
	SLAVE=yes
	USERCTL=no

###现在配置vlan，上联交换机端口为trunk

安装vconfig,加载8021q模块

	root@pts/1 # yum install vconfig
	root@pts/1 # modprobe 8021q
	root@pts/1 # lsmod |grep -i 8021q

在接口上配置vlan

	root@pts/1 # vconfig add bond0 2

> 添加bond0到vlan2

	root@pts/1 # cat ifcfg-bond0.2 
	VLAN=yes
	DEVICE=bond0.2
	BOOTPROTO=static
	NETMASK=255.255.252.0
	ONBOOT=yes
	USERCTL=no
	BONDING_OPTS="mode=1 miimon=50"
	IPADDR=192.168.0.100
	GATEWAY=192.168.0.1

> 创建ifcfg-bond0.2文件；2表示vlan号

重启网卡

	root@pts/1 # /etc/init.d/network restart

查看vlan2参数

	root@pts/1 # cat /proc/net/vlan/bond0.2 
	bond0.2  VID: 2  REORDER_HDR: 1  dev->priv_flags: 1
	         total frames received     18263089
	          total bytes received   7996982934
	      Broadcast/Multicast Rcvd       243416
	
	      total frames transmitted     15313656
	       total bytes transmitted   1663256587
	            total headroom inc            0
	           total encap on xmit            0
	Device: bond0
	INGRESS priority mappings: 0:0  1:0  2:0  3:0  4:0  5:0  6:0 7:0
	 EGRESS priority mappings: 

> 配置vlan后只能同网段相互通信，如果想跨网段，需要配置策略路由

###配置策略路由

添加路由表

	root@pts/1 # vim /etc/iproute2/rt_tables    
	#
	# reserved values
	#
	255     local
	254     main
	253     default
	251     bond15
	250     bond2
	0       unspec
	#
	# local
	#
	#1      inr.ruhep

> 
如上添加了bond15与bond2,前边数值越小，优先级越高
> 
Linux最多可以支持255张路由表，其中有3张表是内置的：
> 
1、表255 本地路由表（Local table） 本地接口地址，广播地址，已及NAT地址都放在这个表。该路由表由系统自动维护，管理员不能直接修改。
> 
2、表254 主路由表（Main table） 如果没有指明路由所属的表，所有的路由都默认都放在这个表里，一般来说，旧的路由工具（如route）所添加的路由都会加到这个表。一般是普通的路由。
> 
3、表253 默认路由表 （Default table） 一般来说默认的路由都放在这张表，但是如果特别指明放的也可以是所有的网关路由。
> 
4、表 0 保留

为了方便以后管理，将添加路由写成脚本

	root@pts/1 # cat pbr-bond2
	ip route flush table bond2
	ip route add default via 192.168.0.1 table bond2
	ip rule add from 192.168.0.100 table bond2

> 脚本名称可以自定义；但是表名table bond2 必须与rt_tables表添加的一致

然后执行

	root@pts/1 # bash pbr-bond2

查看路由表

	root@pts/1 # ip route list table bond2
	default via 192.168.0.1 dev bond0.2

> 
如上有结果证明添加成功，可在别的服务器ping测试