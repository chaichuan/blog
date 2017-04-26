---
layout: post
title:  "CentOS mailx client"
date:   2016-3-23 星期三  13:21:21   
categories: jekyll update
---

想通过Linux监控系统状况，并邮件自动报警，可以使用脚本处理，通过自带的mailx工具来发出警告 邮件

###安装mailx###

	root@pts/1 # yum -y install mail
	Loaded plugins: fastestmirror
	Determining fastest mirrors
	 * base: mirrors.163.com
	 * extras: mirrors.yun-idc.com
	 * updates: mirrors.yun-idc.com
	base                                                                                                                    | 3.7 kB     00:00     
	extras                                                                                                                  | 3.4 kB     00:00     
	updates                                                                                                                 | 3.4 kB     00:00     
	updates/primary_db                                                                                                      | 4.0 MB     00:00     
	Setting up Install Process
	Resolving Dependencies
	--> Running transaction check
	---> Package mailx.x86_64 0:12.4-8.el6_6 will be installed
	--> Finished Dependency Resolution
	
	Dependencies Resolved
	
	===============================================================================================================================================
	 Package                         Arch                             Version                                 Repository                      Size
	===============================================================================================================================================
	Installing:
	 mailx                           x86_64                           12.4-8.el6_6                            base                           235 k
	
	Transaction Summary
	===============================================================================================================================================
	Install       1 Package(s)
	
	Total download size: 235 k
	Installed size: 452 k
	Downloading Packages:
	mailx-12.4-8.el6_6.x86_64.rpm                                                                                           | 235 kB     00:00     
	Running rpm_check_debug
	Running Transaction Test
	Transaction Test Succeeded
	Running Transaction
	  Installing : mailx-12.4-8.el6_6.x86_64                                                                                                   1/1 
	  Verifying  : mailx-12.4-8.el6_6.x86_64                                                                                                   1/1 
	
	Installed:
	  mailx.x86_64 0:12.4-8.el6_6                                                                                                                  
	
	Complete!

确认安装结果（版本必须是12.4以上，不然发邮件需要sendmail服务）

	root@pts/1 # rpm -qa|grep mailx
	mailx-12.4-8.el6_6.x86_64

	root@pts/1 # mail -V
	12.4 7/29/08

###配置外部邮箱（发件人）###

	root@pts/0 # vim /etc/mail.rc
	
	set from=test@139.com smtp=smtp.139.com
	set smtp-auth-user=test@139.com smtp-auth-password=******

在最后添加两行配置

第一行指明使用的外部邮箱与smtp服务器，第二行指明外部邮箱的用户名密码

###测试###

	root@pts/0 # echo "test"|mail -s "test OK" test@youyuan.com 
	或
	root@pts/0 # /bin/mail -s "server log" chaic@youyuan.com < /share/perdir/chaichuan/tools/coll_log/server_log.txt

###带附件的邮件发送###

	root@pts/0 # mail --help
	mail: illegal option -- -
	Usage: mail -eiIUdEFntBDNHRV~ -T FILE -u USER -h hops -r address -s SUBJECT -a FILE -q FILE -f FILE -A ACCOUNT -b USERS -c USERS -S OPTION users


	mail -s "server log" -a 2016_03_23_6CU348591S_192.168.1.55.zip chaic@youyuan.com < /share/perdir/chaichuan/tools/coll_log/server_log.txt