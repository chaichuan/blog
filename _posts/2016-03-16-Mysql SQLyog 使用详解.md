---
layout: post
title:  "Mysql SQLyog 使用详解"
date:   2016-3-16 星期三  10:51:33 
categories: jekyll update
---

###如何创建数据库###

第一步，右键根结点->创建数据库。

![](http://7xppz2.com1.z0.glb.clouddn.com/40.png)
 
第二步，填写数据库名称，选择默认字符集等后点确定即可。

![](http://7xppz2.com1.z0.glb.clouddn.com/41.png)
 

###如何备份和还原数据库###

备份数据库的步骤

第一步：右键要备份的数据库->备份/导出->备份数据库。

![](http://7xppz2.com1.z0.glb.clouddn.com/42.png)
 
第二步，在SQL转储界面上根据自己需要进行设置。

![](http://7xppz2.com1.z0.glb.clouddn.com/43.png)

> 1：可根据需要备份数据、结构或者两者。

> 2：选择备份文件存储的位置。

> 3：对自己要备份的内容进行勾选。

> 4：根据自己需要选择写入文件的内容，如Create Database，Use Database等语句。

第三步，点击导出即可。

###还原数据库的步骤###

第一步，右键要还原到的数据库->导入->执行SQL脚本。

![](http://7xppz2.com1.z0.glb.clouddn.com/44.png)

第二步，在还原数据库界面选择以前备份好的文件。

![](http://7xppz2.com1.z0.glb.clouddn.com/45.png)

第三步，点击‘执行’即可。

###关于数据库表的一些操作###

3.1	备份表数据

右键选择某数据库下的表->备份表作为SQL转储。

![](http://7xppz2.com1.z0.glb.clouddn.com/46.png)

以下步骤可以参考备份数据库的方法。

3.2	还原表数据

与还原数据库的步骤相同。

3.3	如何查看并修改表结构和索引

第一步，右键表->改变表，打开关于表的详细信息。

![](http://7xppz2.com1.z0.glb.clouddn.com/47.png)

第二步，在打开的界面，修改表结构和索引等信息。比如，修改把索引属性修改为FULLTEXT的图如下。

![](http://7xppz2.com1.z0.glb.clouddn.com/48.png)

###如何创建用户及授权用户对数据库操作###

第一种方法：在SQLyog中添加用户并进行授权。

第一步，以root账户的身份创建新连接。

第二步，选择工具->用户管理。

![](http://7xppz2.com1.z0.glb.clouddn.com/49.png)
 
第二步,填写用户名，选择主机localhost或%，输入密码，保存改动即可创建用户。

![](http://7xppz2.com1.z0.glb.clouddn.com/50.png)
 
第三步，在第二步保存改动后右上角用户即相应的改为iyou@%或iyou@localhost（图中1）；然后再选择要授权的数据库，比如是exjc6（图中2）；接下来通过在具体操作前进行勾选给选择的数据库exjc6进行授予该操作的权利。

![](http://7xppz2.com1.z0.glb.clouddn.com/51.png)
 
第二种方法：在命令窗口中进行创建用户和授权。

使用命令进行数据库授权步骤如下：

	@>mysql –u root –p
	mysql>grant all privileges on exjc6.* to iyou@’%’ (identified by PASSWORD(‘123’));
	mysql>flush privileges;

> 说明：

> all表示所有权限(包括增、删、改、查等权限);

> exjc6.*表示对exjc6数据库有所有操作权限， *.* 表示对所有数据库有所有操作权限；

> iyou为添加的用户名；

> 123为密码；

> %为匹配的所有主机，即所有主机都可访问该exjc6数据库，localhost表示匹配本机；

> 执行完会在mysql.user表插入一条记录,并添加一个名为iyou的用户；

> 上面的信息可以指定如:grant select,update on exjc6.* to iyou@localhost identified by '123"。

###如何比较两个数据库之间的的差别并进行同步###

第一步，选择高级工具->架构同步工具。

![](http://7xppz2.com1.z0.glb.clouddn.com/52.png)
 
第二步，选择要比较的两个数据库，要修改的数据库放在右侧的目标源中。

![](http://7xppz2.com1.z0.glb.clouddn.com/53.png)
 
第三步，根据需要可以选择仅比较表格或比较全部对象（表、函数、触发器、存储过程等），然后单击比较，即可出现比较的结果。结果中通过具体颜色标注出创建或修改的对象，并在左下角显示出具体的内容。

第四步，点击执行所有，即可把右侧数据库同步成与左侧数据库结构相同的数据库。需要说明的是这种同步只包含表结构，不包含表数据。

###SQLyog过期，如何破解###

我使用的是SQLyog 9.63，只有一个月的试用期，如何超过了试用期，怎么办？

在这时我可以告诉大家一个解决方案，那就是通过删除注册表项，再获得一个月的试用期。具体的操作方法如下:

第一步，运行窗口中输入regedit，回车进入注册表编辑器。

第二步，按路径HKEY_CURRENT_USER->SoftWare找到红色标注的{8E919370-*}项。

![](http://7xppz2.com1.z0.glb.clouddn.com/54.png)
 
第三步，右键该项，选择删除，再点击是即可。

![](http://7xppz2.com1.z0.glb.clouddn.com/55.png)
 


