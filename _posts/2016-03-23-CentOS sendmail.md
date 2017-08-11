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
	set smtp-auth=login

说明：

	from: 对方收到邮件时显示的发件人
	smtp: 指定第三方发送邮件的smtp服务器地址
	smtp-auth: SMTP的认证方式。默认是LOGIN，也可改为CRAM-MD5或PLAIN方式
	smtp-auth-user: 第三方发邮件的用户名
	smtp-auth-password: 用户名对应密码

###mail命令###

	root@pts/2 # mail --help
	mail: illegal option -- -
	Usage: mail -eiIUdEFntBDNHRV~ -T FILE -u USER -h hops -r address -s SUBJECT -a FILE -q FILE -f FILE -A ACCOUNT -b USERS -c USERS -S OPTION users

注：部分系统参数稍有差异，最好看帮助

###1)  无邮件正文###

  mail -s "主题"  收件地址

	mail -s "测试"  1968089885@foxmail.com
 

###2) 有邮件正文###

 mail -s "主题"  收件地址< 文件(邮件正文.txt)

	mail -s "邮件主题"  1968089885@foxmail.com < /data/findyou.txt

 echo "邮件正文" | mail -s 邮件主题  收件地址

	echo "邮件正文内容" | mail -s "邮件主题"  1968089885@foxmail.com

 cat 邮件正文.txt | mail -s 邮件主题  收件地址 

	cat  /data/findyou.txt | mail -s "邮件主题"  1968089885@foxmail.com
 

###3)  带附件###

 mail -s "主题"  收件地址  -a 附件 < 文件(邮件正文.txt) 

	mail -s "邮件主题"  1968089885@foxmail.com -a /data/findyou.tar.gz < /data/findyou.txt