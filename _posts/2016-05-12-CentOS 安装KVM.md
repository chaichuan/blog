---
layout: post
title:  "CentOS 安装KVM"
date:   2016-5-12 14:44:01 
categories: jekyll update
---

系统要求：

处理器需求：需要一台可以运行最新linux内核的Intel处理器（含VT虚拟化技术）或AMD处理器（含SVM安全虚拟机技术的AMD处理器，也叫AMD-V）

检查CPU：

	root@pts/0 # egrep ‘vmx|svm’ /proc/cpuinfo

如果输出的结果包含VMX，它是Intel处理器虚拟机技术标志：如果包含SVM，它是AMD处理器虚拟机技术的标志，如果你什么都没有得到，那应你的系统并没有支持虚拟化的处理，不能使用KVM，另处linux发行版本必须在64bit环境中才能使用KVM

更新系统

	yum update
	yum upgrade


###一、检查是否安装了图形###

	yum grouplist 

Installed Groups: 里面要有
Desktop 和 X Window System

如果没有安装，现在安装

1.显示系统已经安装的组件，和可以安装的组件:

	yum grouplist|grep -i "x window"

2.如果系统安装之初采用最小化安装，没有安装xwindow，那么先安装：

	yum groupinstall "X Window System"

3.安装GNOME桌面环境

	yum groupinstall "GNOME Desktop Environment"
	yum groupinstall  "Desktop" "Desktop Platform"

4.安装KDE桌面环境

	yum groupinstall "KDE (K Desktop Environment)"
	yum groupinstall "KDE Desktop"

5.卸载GNOME桌面环境

	yum groupremove "GNOME Desktop Environment"

6.卸载KDE桌面环境

	yum groupremove "KDE (K Desktop Environment)"

开机为文本界面，由文本界面切换到图形界面：

方法1：运行命令

	#startx ， 需要先配置图形界面信息

方法2：修改/etc/inittab文件中的

	id:3:initdefault ， 将3改为5 ，重新启动系统；

方法3：进入图形界面： init 5

从图形界面进入文本界面： init 3

重启： init 6
关机： init 3

真机环境中，在图形界面和文本界面间快捷键切换：

Ctrl+Alt+F(n) , 其中F(n)为F1-F6 ，为6个控制台；

Ctrl+ALT+F7 ;

eg:CTRL+ALT+F1是进入文本界面，CTRL+ALT+F7才是图形界面。

###二、检查是否安装vnc###

	root@pts/0 # rpm -q vnc vnc-server
	package vnc is not installed
	package vnc-server is not installed

如果没有，开始安装

	yum install tigervnc tigervnc-server

设置 VNC 密码

安装完成后 需初启动VNC 否则执行第一步 Centos 依然会报未安装VNC

	vncserver
	Password:
	Verify:

设置 VNC密码，输入：（在某用户下输入则登陆用户就是它，如果su imiss切换到imiss用户，输入vncserver则启动的是另一个桌面，同样要设置密码，以后在该账户下启动的vncserver登陆就需要在此用户下设置的密码了。）
此时会提示你输入密码，因为是第一次配置。重复输入两次即可。

	vncpasswd
	Password:
	Verify:

修改/root/.vnc/xstartup文件

由于我们这里使用的centos系统，安装的是gnome桌面

	sed -i 's/twm &/gnome-session &/' /root/.vnc/xstartup

修改/etc/sysconfig/vncservers文件

在这里文件中可以设置希望运行VNC桌面的用户，以及使用vncview连接时显示的屏幕分辨率。VNCSERVERS中的1表示VNC以桌面1运行，如果希望添加其他的桌面，可以修改配置

	vim /etc/sysconfig/vncservers
	添加两行
	VNCSERVERS="1:root"
	VNCSERVERARGS[1]="-geometry 1024x768"

设置为开机启动

	chkconfig --add vncserver
	chkconfig vncserver on

重启vncserver

	root@pts/0 # /etc/init.d/vncserver restart
	Shutting down VNC server: 1:root                           [  OK  ]
	Starting VNC server: 1:root 
	New '192.168.68.221:1 (root)' desktop is 192.168.68.221:1
	
	Starting applications specified in /root/.vnc/xstartup
	Log file is /root/.vnc/192.168.68.221:1.log
	
	                                                           [  OK  ]
停止vnc server：

	vncserver -kill :桌面号
	例如
	vncserver -kill :1

> 注：vncserver只能由启动它的用户来关闭，即时是root也不能关闭其它用户开启的vncserver，除非用kill命令杀死进程


设置同一个显示端口可以连接多个客户机
	
	vncserver -alwaysshared

防火墙端口开放

	/sbin/iptables -I INPUT -p tcp --dport 5901 -j ACCEPT 
	/etc/rc.d/init.d/iptables save

###三、网卡配置###

做桥接 的时候NetworkManager服务必须关闭,和桥接有冲突

	root@pts/0 # /etc/init.d/NetworkManager stop
	root@pts/0 # chkconfig NetworkManager off
	
开始配置

	root@pts/1 # cd /etc/sysconfig/network-scripts/
	root@pts/1 # cp ifcfg-eth0 ifcfg-br0
	root@pts/1 # vim ifcfg-br0
	
	DEVICE="br0"
	TYPE="Bridge"
	BOOTRPOTO="static"
	IPADDR="192.168.68.221"
	NETMASK="255.255.255.0"
	GATEWAY="192.168.68.1"
	ONBOOT="yes"

	root@pts/1 # vim ifcfg-eth0
	
	DEVICE="eth0"
	ONBOOT="yes"
	TYPE="Ethernet"
	BRIDGE="br0"

如果配置了bond0，需要删除

	cd /sys/class/net/
	echo -bond0 > bonding_masters

> 备注：echo后的 - 号表示删除设备， + 号表示添加设备

检查：ls -l /proc/net/bonding是否删除成功，重复第一步操作

重启网卡

	root@pts/0 # /etc/init.d/network restart
	Shutting down interface eth0:  bridge br0 does not exist!
	                                                           [  OK  ]
	Shutting down loopback interface:                          [  OK  ]
	Bringing up loopback interface:                            [  OK  ]
	Bringing up interface eth0:                                [  OK  ]
	Bringing up interface br0:  Determining if ip address 192.168.68.221 is already in use for device br0...
	                                                           [  OK  ]

	root@pts/0 # ifconfig 
	br0       Link encap:Ethernet  HWaddr 74:D4:35:D9:95:00  
	          inet addr:192.168.68.221  Bcast:192.168.68.255  Mask:255.255.255.0
	          inet6 addr: fe80::76d4:35ff:fed9:9500/64 Scope:Link
	          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
	          RX packets:133 errors:0 dropped:0 overruns:0 frame:0
	          TX packets:25 errors:0 dropped:0 overruns:0 carrier:0
	          collisions:0 txqueuelen:0 
	          RX bytes:43220 (42.2 KiB)  TX bytes:5182 (5.0 KiB)
	
	eth0      Link encap:Ethernet  HWaddr 74:D4:35:D9:95:00  
	          inet6 addr: fe80::76d4:35ff:fed9:9500/64 Scope:Link
	          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
	          RX packets:82534 errors:0 dropped:0 overruns:0 frame:0
	          TX packets:54417 errors:0 dropped:0 overruns:0 carrier:0
	          collisions:0 txqueuelen:1000 
	          RX bytes:94214955 (89.8 MiB)  TX bytes:20309804 (19.3 MiB)
	
	lo        Link encap:Local Loopback  
	          inet addr:127.0.0.1  Mask:255.0.0.0
	          inet6 addr: ::1/128 Scope:Host
	          UP LOOPBACK RUNNING  MTU:65536  Metric:1
	          RX packets:3184 errors:0 dropped:0 overruns:0 frame:0
	          TX packets:3184 errors:0 dropped:0 overruns:0 carrier:0
	          collisions:0 txqueuelen:0 
	          RX bytes:264012 (257.8 KiB)  TX bytes:264012 (257.8 KiB)


> 附加说明：这里配置成桥接方式，实际上本机的数据包发送是通过br0发送的，br0的TYPE是Bridge,其它的信息跟配置一个物理网卡一样，本机的eth0通过桥接到br0（通过BRIDGE指定）进行通信，它根本不需要配置IP信息，实际上数据发送还是通过本机的物理网卡


###四、开始安装kvm###

> KVM虚拟机的管理工具
> 
> 准确的来说，KVM仅仅是Linux内核的一个模块，管理和创建完整的KVM虚拟机，需要更多的辅助工具。
> 
> QEMU-KVM：在Linux系统中，首先我们可以用modprobe命令加载KVM模块，如果用RPM安装KVM软件包，系统会在启动时自动加载模块，QEMU是一个强大的虚拟软件，它可以虚拟不同的构架。
> 
> Virt-manager：尽管QEMU-KVM工具可以创建和管理KVM虚拟机，RedHat为KVM开发了更多的辅助工具，比如 libvirt libguestfs等，原因是QEMU工具效率不高，不易于使用

安装kvm

	root@pts/0 # yum -y install kvm python-virtinst libvirt  bridge-utils virt-manager qemu-kvm-tools  virt-viewer  virt-v2v
	
	root@pts/0 # vim /etc/selinux/config 
	...
	SELINUX=disabled
	...
	root@pts/0 # reboot 
	
重启后确认libvirtd是否正常启动

	chkconfig libvirtd on
	/etc/init.d/libvirtd start

检查KVM是否加载成功

显示结果如下表示kvm模块已经加载，如果没有输出信息，则表示没有加载成功：

	root@pts/0 # lsmod |grep kvm
	kvm_intel              55496  0 
	kvm                   337772  1 kvm_intel

检查KVM是否成功安装

	root@pts/0 # virsh list --all   ##--all关闭也会显示
	 Id    Name                           State
	----------------------------------------------------

版本查询

	root@pts/0 # virt-install --version
	0.600.0
	root@pts/0 # virsh --version
	0.10.2

修改qemu.conf，打开vnc监听端口

	root@pts/0 # vim /etc/libvirt/qemu.conf 
	...
	vnc_listen = "0.0.0.0"
	...

查看桥接网络
	
	root@pts/0 # brctl show
	bridge name     bridge id               STP enabled     interfaces
	br0             8000.74d435d99500       no              eth0
	virbr0          8000.525400fad98a       yes             virbr0-nic

> 接口eth0通过br0进行桥接。birbr0-nic通过virbr0进行桥接，这个是默认的设置，当使用NAT时使用

使用virt-install 工具安装guest 系统

	root@pts/0 # virt-install --name=Centos_test --ram 1024 --vcpus=2 --disk path=/var/lib/libvirt/images/centos_test.img,size=20 --accelerate --cdrom /opt/soft/CentOS-6.6-x86_64-bin-DVD1.iso --graphics vnc,password=youyuan,port=5920 --network bridge=br0 --force --autostart

	Starting install...
	Allocating 'centos_test.img'                                                                                                      |  20 GB     00:00     
	Creating domain...                                                                                                                |    0 B     00:00     
	Cannot open display: 
	Run 'virt-viewer --help' to see a full list of available command line options
	Domain installation still in progress. You can reconnect to 
	the console to complete the installation process.

> 使用virt-install 工具，工具自动创建磁盘，默认是raw格式


	root@pts/0 # qemu-img info /var/lib/libvirt/images/centos_test.img
	image: /var/lib/libvirt/images/centos_test.img
	file format: raw
	virtual size: 20G (21474836480 bytes)
	disk size: 0

进入vnc,可以安装了

![](http://7xppz2.com1.z0.glb.clouddn.com/76.png)


使用virt-install 工具安装虚拟机后，在目录/etc/libvirt/qemu/下生成xml 配置文件。

	root@pts/0 # cd /etc/libvirt/qemu
	root@pts/0 # cat Centos_test.xml 
	<domain type='kvm'>                                        虚拟机类型
	    <name>centos</name>                                    虚拟机名称
	    <uuid>54da30ed-150d-514e-9803-4dc01bd50488</uuid>      uuid唯一标示
	    <memory unit='KiB'>1048576</memory>                    指定虚拟机内存大小，给出了单位
	    <vcpu placement='static'>2</vcpu>                      虚拟机占用虚拟cpu个数，这里指物理cpu的核心数量
	    <os>
	      <type arch='x86_64' machine='rhel6.3.0'>hvm</type>   指定虚拟系统架构
	      <boot dev='hd'/>                                     启动类型，从硬盘启动
	    </os>
	    <devices>
	      <emulator>/usr/libexec/qemu-kvm</emulator>           驱动程序，同上，使用的是qemu-kvm
	      <disk type='file' device='disk'>                     指定磁盘类型
	      <driver name='qemu' type='raw' cache='none'/>        指定磁盘格式，这里是raw，也支持qcow2.
	      <source file='/home/vms/centos.img'/>                img文件路径
	      <target dev='hda' bus='ide'/>                        磁盘文件标示，驱动类型
	      <address type='drive' controller='0' bus='0' target='0' unit='0'/>
	      <interface type='bridge'>
	        <mac address='52:54:00:11:57:e6'/>                 自动生成，可以手动指定。
	        <source bridge='br0'/>                             桥接到哪一个接口
	      </interface>
	    </devices>
	</domain>

使用virsh工具管理生成的虚拟机

	root@pts/0 # virsh start centos64   ##启动虚拟机 reboot  start  shutdown
	root@pts/0 # virsh list             ##列出当前运行的虚拟机
	Id    Name                           State
	----------------------------------------------------
	1     centos64                       running
	root@pts/0 # ll  /var/lib/libvirt/qemu
	total 12
	srwxr-xr-x 1 root root    0 Aug 22 09:53 centos.monitor  ##这里的文件，是libvirtd在重启后，虚拟机也随着自动重启。

动态查询kvm资源占用情况，每一台VM，对应一个qemu-kvm进程

	root@pts/0 # top -d 1 | grep kvm

截取的kvm进程信息

	root@pts/0 # ps -aux | grep kvm

其他问题

1、图形化配置：从菜单中启动，或者运行virt-manager进入图形管理界面，比较简单，不过img的位置需要注意一下，kvm安装后会有一个默认位置，不注意的话容易占满空间。或者直接硬盘分区时单独给/var分一个区。

2、虚拟机重新配置：虚拟机的配置文件保存在/etc/libvirt/qemu目录下，扩展名是xml。修改相应的文件即可重新配置虚拟机。

3、虚拟机备份及恢复：

备份：拷贝/etc/libvirt/qemu目录下的xml配置文件，以及xml中设置的img文件。
恢复：进入virsh，执行define 目录下的xml文件路径

	virsh # define /etc/libvirt/qemu/winxp.xml

4、开机自动启动虚拟机：

	root@pts/0 # virsh autostart Winxp

5、克隆KVM虚拟机：

	root@pts/0 # virt-clone -o Winxp -n winxpclong -f /var/lib/libvirt/images/winxpclong.img

> （-o 原始客体的名称，-n新客户端的名称，-f作为新客户端磁盘映像的新文件）

6、虚拟机意外关机报下述错误：

	root@pts/0 # virsh start winxp
	错误：开始域 winxp 失败
	错误：Unable to read from monitor: Connection reset by peer

解决方法：virsh managedsave-remove winxp

	Virsh语法参考：
	Autostart                         自动开始一个域
	Create                            从一个 XML 文件创建一个域
	Define                            从一个 XML 文件定义（但不开始）一个域
	edit                              编辑某个域的 XML 配置
	shutdown                          关闭一个域
	start                             开始一个（以前定义的）非活跃的域
	reboot                            重新启动一个域
	suspend                           挂起一个域
	resume                            重新恢复一个域
	vncdisplay                        vnc 显示

使用save来备份当前虚拟机的状态：

	root@pts/0 # virsh save –bypass-cache RedHat /var/lib/libvirt/images/test.img –running

上面这个命令将RedHat的当前状态保存到/opt/vm2_save文件中。–running参数表示下次restore回来的时候能够自动启动RedHat。

这个命令会导致RedHat被关闭。

现在还原：必须先关闭虚拟机

	root@pts/0 # virsh shutdown RedHat
	root@pts/0 # virsh restore /var/lib/libvirt/images/test.img –bypass-cache –running （还原时不要指定虚拟机名称）

7、kvm 虚拟机VNC下鼠标不同步问题解决方法

在虚拟机的配置文件中增加：

	<input type=’tablet’ bus=’usb’/>

（该句位于\<devices>配置中）

加上这个输入设备，重启虚拟机后VNC鼠标与系统鼠标同步问题解决!

8、在KVM中克隆出新的CentOS虚拟机时，出现如下问题：

	service network restart

	Shutting down loopback insterface: [ OK ]
	Bringing up loopback insterface: [ OK ]
	Bringing up interface eth0: Device eth0 does not seem to be present,delaying initialization. [FAILED]

用ifconfig查看发现缺少eth0，只有lo；用ifconfig -a查看发现多出了eth1的信息。

解决办法1：

	mv /etc/sysconfig/network-scripts/ifcfg-eth0  /etcsysconfig/network-scripts/ifcfg-eth1

将eth0的mac地址改为eth1的mac地址，同时改变其DEVICE名称为eth1，再重启网络即可。

解决办法2：

	rm -rf /etc/udev/rules.d/70-persistent-net.rules
	reboot

总之，只要保证/etc/sysconfig/network-scripts/ifcfg-eth0 与/etc/udev/rules.d/70-persistent-net.rules的信息一致即可，即网卡地址与网卡编号一致，这样service network restart 就可以配置成功

9、"Error, some other host already uses address"的解决方案

解决方法如下：

	vim /etc/sysconfig/network-scripts/ifup-eth
	         
	#if ! /sbin/arping -q -c 2 -w 3 -D -I ${REALDEVICE} ${IPADDR} ; then  
	#   echo {1}quot;Error, some other host already uses address ${IPADDR}."  
	#   exit 1  
	#fi  

注解掉下面的几行内容,然后再执行service network restart

>（备注：可以在vim编辑器中使用“/arping”之类的关键词快速定位）

