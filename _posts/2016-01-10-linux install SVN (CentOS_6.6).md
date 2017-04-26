---
layout: post
title:  "linux install SVN (CentOS_6.6)"
date:   2016-1-10 星期日  21:06:28 
categories: jekyll update
---


###一、环境准备###

system：CentOS_6.6

IP：192.168.30.129

安装yum：

	rpm -Uhv http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm

###二、安装SVN###

	root@pts/0 # yum -y install subversion
	Loaded plugins: fastestmirror, security
	Setting up Install Process
	Determining fastest mirrors
	epel/metalink                                                                                                     | 5.0 kB     00:00     
	 * base: mirrors.btte.net
	 * epel: mirrors.opencas.cn
	 * extras: mirrors.btte.net
	 * updates: mirrors.btte.net
	base                                                                                                              | 3.7 kB     00:00     
	base/primary_db                                                                                                   | 4.6 MB     00:06     
	epel                                                                                                              | 4.3 kB     00:00     
	epel/primary_db                                                                                                   | 5.7 MB     00:44     
	extras                                                                                                            | 2.9 kB     00:00     
	extras/primary_db                                                                                                 |  33 kB     00:00     
	updates                                                                                                           | 3.4 kB     00:00     
	updates/primary_db                                                                                                | 3.3 MB     00:04     
	Resolving Dependencies
	--> Running transaction check
	---> Package subversion.x86_64 0:1.6.11-15.el6_7 will be installed
	--> Finished Dependency Resolution
	
	Dependencies Resolved
	
	=========================================================================================================================================
	 Package                          Arch                         Version                               Repository                     Size
	=========================================================================================================================================
	Installing:
	 subversion                       x86_64                       1.6.11-15.el6_7                       updates                       2.3 M
	
	Transaction Summary
	=========================================================================================================================================
	Install       1 Package(s)
	
	Total download size: 2.3 M
	Installed size: 12 M
	Downloading Packages:
	subversion-1.6.11-15.el6_7.x86_64.rpm                                                                             | 2.3 MB     00:02     
	warning: rpmts_HdrFromFdno: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
	Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
	Importing GPG key 0xC105B9DE:
	 Userid : CentOS-6 Key (CentOS 6 Official Signing Key) <centos-6-key@centos.org>
	 Package: centos-release-6-6.el6.centos.12.2.x86_64 (@anaconda-CentOS-201410241409.x86_64/6.6)
	 From   : /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
	Running rpm_check_debug
	Running Transaction Test
	Transaction Test Succeeded
	Running Transaction
	Warning: RPMDB altered outside of yum.
	  Installing : subversion-1.6.11-15.el6_7.x86_64                                                                                     1/1 
	  Verifying  : subversion-1.6.11-15.el6_7.x86_64                                                                                     1/1 
	
	Installed:
	  subversion.x86_64 0:1.6.11-15.el6_7                                                                                                    
	
	Complete!

输入rpm -ql subversion查看安装位置

	root@pts/0 # rpm -ql subversion
	/etc/bash_completion.d
	/etc/bash_completion.d/subversion
	/etc/rc.d/init.d/svnserve
	/etc/subversion
	/usr/bin/svn
	/usr/bin/svnadmin
	/usr/bin/svndumpfilter
	/usr/bin/svnlook
	/usr/bin/svnserve
	/usr/bin/svnsync
	/usr/bin/svnversion
	....

查看svn 帮助

	root@pts/0 # svn --help
	usage: svn <subcommand> [options] [args]
	Subversion command-line client, version 1.6.11.
	Type 'svn help <subcommand>' for help on a specific subcommand.
	Type 'svn --version' to see the program version and RA modules
	  or 'svn --version --quiet' to see just the version number.
	
	Most subcommands take file and/or directory arguments, recursing
	on the directories.  If no arguments are supplied to such a
	command, it recurses on the current directory (inclusive) by default.
	
	Available subcommands:
	   add
	   blame (praise, annotate, ann)
	   cat
	   changelist (cl)
	   checkout (co)
	   cleanup
	   commit (ci)
	   copy (cp)
	   delete (del, remove, rm)
	   diff (di)
	   export
	   help (?, h)
	   import
	   info
	   list (ls)
	   lock
	   log
	   merge
	   mergeinfo
	   mkdir
	   move (mv, rename, ren)
	   propdel (pdel, pd)
	   propedit (pedit, pe)
	   propget (pget, pg)
	   proplist (plist, pl)
	   propset (pset, ps)
	   resolve
	   resolved
	   revert
	   status (stat, st)
	   switch (sw)
	   unlock
	   update (up)
	
	Subversion is a tool for version control.
	For additional information, see http://subversion.tigris.org/

	root@pts/0 # svnadmin --help
	general usage: svnadmin SUBCOMMAND REPOS_PATH  [ARGS & OPTIONS ...]
	Type 'svnadmin help <subcommand>' for help on a specific subcommand.
	Type 'svnadmin --version' to see the program version and FS modules.
	
	Available subcommands:
	   crashtest
	   create
	   deltify
	   dump
	   help (?, h)
	   hotcopy
	   list-dblogs
	   list-unused-dblogs
	   load
	   lslocks
	   lstxns
	   pack
	   recover
	   rmlocks
	   rmtxns
	   setlog
	   setrevprop
	   setuuid
	   upgrade
	   verify

###三、创建svn版本库目录###

	root@pts/0 # mkdir -p /usr/local/svn/test

###四、创建版本库###

	root@pts/0 # svnadmin create /usr/local/svn/test
	Test_1 [~] 2016-01-10 15:03:28
	root@pts/0 # ll /usr/local/svn/test
	total 24
	drwxr-xr-x. 2 root root 4096 Jan 10 15:03 conf
	drwxr-sr-x. 6 root root 4096 Jan 10 15:03 db
	-r--r--r--. 1 root root    2 Jan 10 15:03 format
	drwxr-xr-x. 2 root root 4096 Jan 10 15:03 hooks
	drwxr-xr-x. 2 root root 4096 Jan 10 15:03 locks
	-rw-r--r--. 1 root root  229 Jan 10 15:03 README.txt

###五、进入conf目录（该svn版本库配置文件）###

	root@pts/0 # cd conf/
	Test_1 [/usr/local/svn/test/conf] 2016-01-10 15:12:38
	root@pts/0 # ll
	total 12
	-rw-r--r--. 1 root root 1080 Jan 10 15:03 authz
	-rw-r--r--. 1 root root  309 Jan 10 15:03 passwd
	-rw-r--r--. 1 root root 2279 Jan 10 15:03 svnserve.conf

authz文件是权限控制文件

passwd是帐号密码文件

svnserve.conf SVN服务配置文件

###六、设置帐号密码###

	root@pts/0 # vim passwd 
	### This file is an example password file for svnserve.
	### Its format is similar to that of svnserve.conf. As shown in the
	### example below it contains one section labelled [users].
	### The name and password for each user follow, one account per line.
	
	[users]
	# harry = harryssecret
	# sally = sallyssecret
	test = test123
	other = test123

在[users]块中添加用户和密码，格式：帐号=密码，如test = test123

###七、设置权限###

	root@pts/0 # vim authz
	....
	[/]
	test = rw
	other = r

意思是版本库的根目录test对其有读写权限，other只有读权限。

###八、修改svnserve.conf文件###

	root@pts/0 # vim svnserve.conf
	....
	anon-access = none
	auth-access = write
	password-db = passwd
	authz-db = authz
	realm = /usr/local/svn/test

	
打开下面的几个注释：

anon-access = none #匿名用户不可访问

auth-access = write #授权用户可写

password-db = passwd #使用哪个文件作为账号文件

authz-db = authz #使用哪个文件作为权限文件

realm = /var/svn/svnrepos # 认证空间名，版本库所在目录

###九、启动svn版本库###

	root@pts/0 # svnserve -d -r /usr/local/svn
	root@pts/0 # ps -ef |grep svn
	root      3082     1  0 15:31 ?        00:00:00 svnserve -d -r /usr/local/svn

###十、测试连接###

	root@pts/0 # mkdir -p /opt/test
	Test_1 [/opt] 2016-01-10 15:37:01
	root@pts/0 # cd /opt/test/
	Test_1 [/opt/test] 2016-01-10 15:37:08
	root@pts/0 # svn co svn://127.0.0.1/test /opt/test/
	Authentication realm: <svn://127.0.0.1:3690> /usr/local/svn/test
	Password for 'root': 
	Authentication realm: <svn://127.0.0.1:3690> /usr/local/svn/test
	Username: test
	Password for 'test': 
	
	-----------------------------------------------------------------------
	ATTENTION!  Your password for authentication realm:
	
	   <svn://127.0.0.1:3690> /usr/local/svn/test
	
	can only be stored to disk unencrypted!  You are advised to configure
	your system so that Subversion can store passwords encrypted, if
	possible.  See the documentation for details.
	
	You can avoid future appearances of this warning by setting the value
	of the 'store-plaintext-passwords' option to either 'yes' or 'no' in
	'/root/.subversion/servers'.
	-----------------------------------------------------------------------
	Store password unencrypted (yes/no)? yes
	Checked out revision 0.

	root@pts/0 # svn info
	Path: .
	URL: svn://127.0.0.1/test
	Repository Root: svn://127.0.0.1/test
	Repository UUID: ec5619f6-c7b2-4018-a560-616f7d6462cc
	Revision: 0
	Node Kind: directory
	Schedule: normal
	Last Changed Rev: 0
	Last Changed Date: 2016-01-10 15:45:56 +0000 (Sun, 10 Jan 2016)

linux svn 更多命令可以查询帮助信息！

###十一、测试上传文件，查看版本###

	root@pts/0 # touch file
	Test_1 [/opt/test] 2016-01-10 16:15:57
	root@pts/0 # echo "svn is test" > file 
	Test_1 [/opt/test] 2016-01-10 16:16:14
	root@pts/0 # svn add file 
	A         file
	Test_1 [/opt/test] 2016-01-10 16:16:21
	root@pts/0 # svn commit file -m 'test'
	Adding         file
	Transmitting file data .
	Committed revision 1.
	Test_1 [/opt/test] 2016-01-10 16:16:56
	root@pts/0 # ll
	total 4
	-rw-r--r--. 1 root root 12 Jan 10 16:16 file
	Test_1 [/opt/test] 2016-01-10 16:17:08
	root@pts/0 # svn info file 
	Path: file
	Name: file
	URL: svn://127.0.0.1/test/file
	Repository Root: svn://127.0.0.1/test
	Repository UUID: ec5619f6-c7b2-4018-a560-616f7d6462cc
	Revision: 1
	Node Kind: file
	Schedule: normal
	Last Changed Author: test
	Last Changed Rev: 1
	Last Changed Date: 2016-01-10 16:16:42 +0000 (Sun, 10 Jan 2016)
	Text Last Updated: 2016-01-10 16:16:14 +0000 (Sun, 10 Jan 2016)
	Checksum: 304b5e95556c9066f5aa1268ede6df6a

显示版本已更新为 1 

###十二、在Windows上测试###

首先安装[TortoiseSVN](http://jingyan.baidu.com/article/19020a0a27f90c529c284259.html)

找地方新建svn目录，右击 - 检出

![](http://7xppz2.com1.z0.glb.clouddn.com/32.png)

添加svn版本库URL 与 检出 目录

![](http://7xppz2.com1.z0.glb.clouddn.com/33.png)

输入账户密码 user: test  passwd: test123

![](http://7xppz2.com1.z0.glb.clouddn.com/34.png)

可以看到刚才服务器提交的file文件已经更新

![](http://7xppz2.com1.z0.glb.clouddn.com/35.png)

Windows更多操作这里不多说，看帮助吧！