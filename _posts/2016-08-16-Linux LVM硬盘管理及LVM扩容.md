---
layout: post
title:  "Linux LVM硬盘管理及LVM扩容"
date:   2016-8-16 11:45:05 
categories: jekyll update
---

LVM磁盘管理

###一、LVM简介###

LVM是 Logical Volume Manager(逻辑卷管理)的简写，它由Heinz Mauelshagen在Linux 2.4内核上实现。LVM将一个或多个硬盘的分区在逻辑上集合，相当于一个大硬盘来使用，当硬盘的空间不够使用的时候，可以继续将其它的硬盘的分区加入其中，这样可以实现磁盘空间的动态管理，相对于普通的磁盘分区有很大的灵活性。

与传统的磁盘与分区相比，LVM为计算机提供了更高层次的磁盘存储。它使系统管理员可以更方便的为应用与用户分配存储空间。在LVM管理下的存储卷可以按需要随时改变大小与移除(可能需对文件系统工具进行升级)。LVM也允许按用户组对存储卷进行管理，允许管理员用更直观的名称(如"sales'、 'development')代替物理磁盘名(如'sda'、'sdb')来标识存储卷。

如图所示LVM模型：

![](http://7xppz2.com1.z0.glb.clouddn.com/77.jpg)

由四个磁盘分区可以组成一个很大的空间，然后在这些空间上划分一些逻辑分区，当一个逻辑分区的空间不够用的时候，可以从剩余空间上划分一些空间给空间不够用的分区使用。

###二、 LVM基本术语###

前面谈到，LVM是在磁盘分区和文件系统之间添加的一个逻辑层，来为文件系统屏蔽下层磁盘分区布局，提供一个抽象的盘卷，在盘卷上建立文件系统。首先我们讨论以下几个LVM术语：

物理存储介质（The physical media）：这里指系统的存储设备：硬盘，如：/dev/hda1、/dev/sda等等，是存储系统最低层的存储单元。

物理卷（physical volume）：物理卷就是指硬盘分区或从逻辑上与磁盘分区具有同样功能的设备(如RAID)，是LVM的基本存储逻辑块，但和基本的物理存储介质（如分区、磁盘等）比较，却包含有与LVM相关的管理参数。

卷组（Volume Group）：LVM卷组类似于非LVM系统中的物理硬盘，其由物理卷组成。可以在卷组上创建一个或多个“LVM分区”（逻辑卷），LVM卷组由一个或多个物理卷组成。

逻辑卷（logical volume）：LVM的逻辑卷类似于非LVM系统中的硬盘分区，在逻辑卷之上可以建立文件系统(比如/home或者/usr等)。

PE（physical extent）：每一个物理卷被划分为称为PE(Physical Extents)的基本单元，具有唯一编号的PE是可以被LVM寻址的最小单元。PE的大小是可配置的，默认为4MB。

LE（logical extent）：逻辑卷也被划分为被称为LE(Logical Extents) 的可被寻址的基本单位。在同一个卷组中，LE的大小和PE是相同的，并且一一对应。

简单来说就是：

PV:是物理的磁盘分区

VG:LVM中的物理的磁盘分区，也就是PV，必须加入VG，可以将VG理解为一个仓库或者是几个大的硬盘。

LV：也就是从VG中划分的逻辑分区

如下图所示PV、VG、LV三者关系：

![](http://7xppz2.com1.z0.glb.clouddn.com/78.jpg)

###三、 安装LVM###

首先确定系统中是否安装了lvm工具：

    [root@www root]# rpm –qa|grep lvm
    lvm-1.0.3-4

如果命令结果输入类似于上例，那么说明系统已经安装了LVM管理工具；如果命令没有输出则说明没有安装LVM管理工具，则需要从网络下载或者从光盘装LVM rpm工具包。

###四、 创建和管理LVM###

要创建一个LVM系统，一般需要经过以下步骤：

1、 创建分区

使用分区工具（如：fdisk等）创建LVM分区，方法和创建其他一般分区的方式是一样的，区别仅仅是LVM的分区类型为8e。如下列图所示：

![](http://7xppz2.com1.z0.glb.clouddn.com/79.jpg)

查看磁盘分区表

![](http://7xppz2.com1.z0.glb.clouddn.com/80.jpg)

使用fdisk创建分区，根据设备文件的绝对路径（/dev/hda）进入分区管理

![](http://7xppz2.com1.z0.glb.clouddn.com/81.jpg)

![](http://7xppz2.com1.z0.glb.clouddn.com/82.jpg)

#####一定要指定分区的格式为8e，这是LVM的分区格式#####

2、 创建PV

![](http://7xppz2.com1.z0.glb.clouddn.com/83.jpg)

3、 创建VG

![](http://7xppz2.com1.z0.glb.clouddn.com/84.jpg)

![](http://7xppz2.com1.z0.glb.clouddn.com/85.jpg)

创建完成VG之后，才能从VG中划分一个LV。

4、 创建LV

![](http://7xppz2.com1.z0.glb.clouddn.com/86.jpg)

创建了一个名字为lvData，容量大小是100M的分区，其中：-L：指定LV的大小 -n：指定LV的名。Vo1Group00：表示从这个VG中划分LV；

5、LV格式化及挂载

下一步需要对LV进行格式化（使用mksf进行格式化操作），然后LV才能存储资料

![](http://7xppz2.com1.z0.glb.clouddn.com/87.jpg)

将格式化后的LV分区挂载到指定的目录下，就可以像普通目录一样存储数据了

![](http://7xppz2.com1.z0.glb.clouddn.com/88.jpg)

挂载之后，可以看到此LV的容量。

如果要在系统启动的时候启动LV，最好是将lvData写入fstable 文件中，如下所示：

![](http://7xppz2.com1.z0.glb.clouddn.com/89.jpg)

使用Vim编辑器，打开/etc/fstab，在最后一行添加如图中所示，其中/dev/VolGroup00/lvData指定需要挂载的分区LV，/root/test指定要挂载的目录（挂载点），ext3分区文件系统格式，其它使用默认即可

##扩容当前分区##

###一、首先创建一块新的分区：###

fdisk  /dev/hda

n

l        #选择逻辑分区，如果没有，则首先创建扩展分区，然后再添加逻辑分区（硬盘：最多四个分区P-P-P-P或P-P-P-E）

6        #分区号（从5开始），/dev/hda6

t      8e   #分区类型8e表示LVM分区

w        #写入分区表

partprobe   #重读分区表

mkfs –t ext3 /dev/hda6 #格式化

partx /dev/hda #查看当前硬盘的分区表及使用情况

###二、创建PV，扩容VG，LV###

pvcreate /dev/hda6

vgdisplay #查看当前已经存在的VG信息，以存在VG：VolGroup00为例

vgextend VolGroup00 /dev/hda6    #扩展VolGroup00

lvdisplay #查看已经存在的LV信息，以存在LV：LogVol01为例

lvextend –L 1G /dev/VolGroup00/LogVol01 #扩展LV

resize2fs /dev/VolGroup00/LogVol01 #执行该重设大小，对于当前正在使用的LogVol01有效

df –h #查看挂载情况，已经扩容