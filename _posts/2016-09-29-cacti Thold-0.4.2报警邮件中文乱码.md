---
layout: post
title:  "cacti Thold-0.4.2报警邮件中文乱码"
date:   2016-9-29 14:51:53 
categories: jekyll update
---

![](http://7xppz2.com1.z0.glb.clouddn.com/90.png)

1、修改thold_functions.php

	vim /var/www/html/plugins/thold/thold_functions.php
	
	$message = str_replace('<SUBJECT>', $subject, $message);
	在这行下边添加一行
	$subject = "=?UTF-8?B?".base64_encode($subject)."?=";

这样主题就可以支持中文了

2、修改polling.php

	vim /var/www/html/plugins/thold/includes/polling.php

查找if ($host['status'] == HOST_UP) {

修改下边两段信息

	/*$subject = 'Host Notice: <DESCRIPTION> (<HOSTNAME>) returned from DOWN state';*/

注释这行，添加下行（或者直接修改，php 注释/*。。。*/）

	$subject = '主机通知:' . $host['description'] . ' 从宕机状态恢复';

/

	/*$msg = '<br>System <DESCRIPTION> (<HOSTNAME>) status: <DOWN/UP><br><br>Current ping response: <CUR_TIME> ms<br>Average system response : <AVG_TIME> ms<br>System availability: <AVAILABILITY><br>Total Checks Since Clear: <TOT_POLL><br>Total Failed Checks: <FAIL_POLL><br>Last Date Checked UP: <LAST_FAIL><br>Host Previously DOWN for: <DOWNTIME><br><br>Snmp Info:<br>Name - <SNMP_HOSTNAM
	E><br>Location - <SNMP_LOCATION><br>Uptime - <UPTIMETEXT> (<UPTIME> ms)<br>System - <SNMP_SYSTEM><br><br>NOTE: <NOTES>';*/

注释这行，添加下两行（或者直接修改，php 注释/*。。。*/）

	$msg = '主机通知：' . $host['description'] . ' (' . $host['hostname'] . ') 从宕机状态恢复';
	$msg = iconv("UTF-8", "GB2312//IGNORE", $msg);

/

	/*$subject = 'Host Error: <DESCRIPTION> (<HOSTNAME>) is DOWN';*/

注释这行，添加下行（或者直接修改，php 注释/*。。。*/）

	$subject = '主机错误:' . $host['description'] . ' 宕机';

/

	/*$msg = 'System Error : <DESCRIPTION> (<HOSTNAME>) is <DOWN/UP><br>Reason: <MESSAGE><br><br>Average system respon
	se : <AVG_TIME> ms<br>System availability: <AVAILABILITY><br>Total Checks Since Clear: <TOT_POLL><br>Total Failed Checks: <FAIL_POLL><br>Last Da
	te Checked DOWN : <LAST_FAIL><br>Host Previously UP for: <DOWNTIME><br>NOTE: <NOTES>';*/

注释这行，添加下两行（或者直接修改，php 注释/*。。。*/）

	$msg = '主机错误：' . $host['description'] . ' (' . $host['hostname'] . ') 宕机<br>错误信息：' . $host['status_last_error'];
	$msg = iconv("UTF-8", "GB2312//IGNORE", $msg);

保存后重启http

	/etc/init.d/httpd restart

![](http://7xppz2.com1.z0.glb.clouddn.com/91.png)
