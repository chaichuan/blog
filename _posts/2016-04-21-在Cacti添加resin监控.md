---
layout: post
title:  "在Cacti添加resin监控"
date:   2016-4-21 15:11:48 
categories: jekyll update
---


	#vim /etc/snmp/snmpd.conf

在snmpd.conf文件末尾添加

	proxy -v 2c -c public 127.0.0.1:1161 .1.3.6.1.4.1.42

修改jdk配置

	# cd $JAVA_HOME/jre/lib/management

	#vim management.properties

	找到#com.sun.management.snmp.port=<port-number>   
	改成com.sun.management.snmp.port=1161 

	找到#com.sun.management.snmp.interface=0.0.0.0   
	改成com.sun.management.snmp.interface=127.0.0.1

	#cp snmp.acl.template snmp.acl   
	

	#vim snmp.acl        -----配置JVM其允许通过snmp访问权限

	找到 

	# acl = {   
	#  {   
	#    communities = public, private   
	#    access = read-only   
	#    managers = localhost 
	#  }   
	# }   
	#    
	#    
	# trap = {   
	#   {   
	#     trap-community = public   
	#     hosts = localhost 
	#   }   
	# }
   
	去掉所有的# , localhost改为本机IP 127.0.0.1

修改权限

	#chmod 600 management.properties snmp.acl

修改resin.xml
	
	# vim /usr/local/resin/conf/resin.xml

	在<thread-max>10240</thread-max>下面对其添加：

	<jvm-arg>-Dcom.sun.management.jmxremote</jvm-arg>
	
	<jvm-arg>-Dcom.sun.management.jmxremote.port=51517</jvm-arg>
	
	<jvm-arg>-Dcom.sun.management.jmxremote.ssl=false</jvm-arg>
	
	<jvm-arg>-Dcom.sun.management.jmxremote.authenticate=false</jvm-arg>
	
	<jvm-arg>-Dcom.sun.management.snmp.port=1161</jvm-arg>

重启服务

	#service snmpd restart
	
	#/usr/local/resin/bin/resin.sh stop -server {server}
	
	# /usr/local/resin/bin/resin.sh start -server {server}