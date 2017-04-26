---
layout: post
title:  "linux yum install mysql"
date:   2016-3-16 星期三  10:06:36 
categories: jekyll update
---

###安装查看有没有安装过：###

	yum list installed mysql*
	rpm -qa | grep mysql* 

###查看有没有安装包：###

	yum list mysql* 

###安装mysql客户端：###

	yum install mysql 

###安装mysql 服务器端：### 

	yum install mysql-server
	yum install mysql-devel

###启动&&停止 数据库字符集设置###

mysql配置文件/etc/my.cnf添加 default-character-set=utf8

	root@pts/0 # vim /etc/my.cnf
	
	[mysqld]
	datadir=/var/lib/mysql
	socket=/var/lib/mysql/mysql.sock
	user=mysql
	# Disabling symbolic-links is recommended to prevent assorted security risks
	symbolic-links=0
	default-character-set=utf8

	[mysqld_safe]
	log-error=/var/log/mysqld.log
	pid-file=/var/run/mysqld/mysqld.pid

启动mysql服务：

	service mysqld start
	或者
	/etc/init.d/mysqld start

开机启动：

添加开机启动：

	chkconfig --level 35 mysqld on

查看开机启动设置是否成功

	chkconfig --list | grep mysql* 

	mysqld          0:关闭  1:关闭  2:关闭  3:启用  4:关闭  5:启用  6:关闭

停止： 

	service mysqld stop

###登录 创建root管理员：###

	mysqladmin -u root password 123456 

登录：

	 mysql -u root -p输入密码即可。

忘记密码：

	service mysqld stop;
	mysqld_safe --user=root --skip-grant-tables;

这一步骤执行的时候不会出现新的命令行，你需要重新打开一个窗口执行下面的命令

	mysql -u root;
	use mysql ;
	update user set password=password("123456") where user="root";
	flush privileges; 

###远程访问 开放防火墙的端口号mysql###

增加权限：

mysql库中的user表新增一条记录host为“%”，user为“root”

###Linux MySQL的几个重要目录 ###

数据库目录 /var/lib/mysql/

配置文件 /usr/share /mysql（mysql.server命令及配置文件）

相关命令 /usr/bin（mysqladmin mysqldump等命令）

启动脚本 /etc/rc.d/init.d/（启动脚本文件mysql的目录）

###删除 mysql 数据库 ###

如果使用的是 yum 安装的 mysql，需要删除的话，就使用如下命令：

	yum -y remove mysql*

然后将 /var/lib/mysql文件夹下的所有文件都删除干净

最后再重新执行上面的安装步骤

###授权用户从远程登录###

#####改表法#####

可能是你的帐号不允许从远程登陆，只能在localhost。这个时候只要在localhost的那台电脑，登入mysql后，更改 "mysql" 数据库里的 "user" 表里的 "host" 项，从"localhost"改称"%"

	mysql -u root -pvmware;
	mysql>use mysql;
	mysql>update user set host = '%' where user = 'root';
	mysql>select host, user from user;

#####授权法#####

例如，你想myuser使用mypassword从任何主机连接到mysql服务器的话。

	GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%' IDENTIFIED BY 'mypassword';

如果你想允许用户myuser从ip为192.168.1.3的主机连接到mysql服务器，并使用mypassword作为密码

	GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.3' IDENTIFIED BY 'mypassword';



【下面这一句一定要执行，否则还是无法登陆】

	mysql>flush privileges ;


如果用户无法从本地登陆，这个时候就执行如下

	GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost.localdomain' IDENTIFIED BY '123456';

别忘了加上如下的语句

	flush privileges ;