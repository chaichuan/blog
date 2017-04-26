---
layout: post
title:  "HP DL380 Gen8 update BIOS"
date:   2016-1-4 星期一  16:01:41    
categories: jekyll update
---


###需求###
HP DL380 Gen8

Centos 6.3

重启升级BIOS、HP-IP固件版本

> 说明：HP-IP固件是指HP重启按键F10进入配置管理界面的版本

###准备（固件IOS可咨询HP下载）###

SPP201504 光盘（BIOS）

HP-IP 光盘（HP IP）

Gen8 HP-IP 下载：

http://h20565.www2.hpe.com/hpsc/swd/public/detail?sp4ts.oid=5194969&swItemId=MTX_6a21f77553cf455a8125f30c77&swEnvOid=4064#tab1


###升级BIOS###

插入spp 15.4 光盘（此升级大约20分钟）

重启服务器，光盘引导，选择自动更新

![](http://7xppz2.com1.z0.glb.clouddn.com/16.png)


等待自动升级

![](http://7xppz2.com1.z0.glb.clouddn.com/17.png)

分三步升级

![](http://7xppz2.com1.z0.glb.clouddn.com/18.png)

升级到第三阶段

![](http://7xppz2.com1.z0.glb.clouddn.com/19.png)

升级完成重启看到BIOS是最新

![](http://7xppz2.com1.z0.glb.clouddn.com/20.png)

至此BIOS固件版本升级成功

###升级HP-IP###

插入HP IP 光盘（此升级大约1分钟）

重启光盘引导会自动升级HP-IP

![](http://7xppz2.com1.z0.glb.clouddn.com/8.png)

等待升级完成会自动重启，按F10进入管理界面查看升级成功

![](http://7xppz2.com1.z0.glb.clouddn.com/9.png)

至此HP-IP升级成功

