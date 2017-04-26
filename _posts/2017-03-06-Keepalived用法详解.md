---
layout: post
title:  "Keepalived用法详解"
date:   2017-3-6 16:05:33 
categories: jekyll update
---

keepalived已经接触了一段时间，它的配置文件格式熟记于心，但不了解配置中每一行参数的意义，至于其代码架构，完全是一窍不通。

编译keepalived的时候，需要注意，如果需要支持lvs的，一定要在./configure上加上参数"–with-kernel-dir=/usr/src/kernels/2.6.18-238.19.1.el5-x86_64"（不同系统内核不同）。

keepalived.conf文件是keepalived的唯一一份配置文件，结构清晰，现在自己注释一遍，加深自己的理解。

	! Configuration File for keepalived
	global_defs {					#全局配置 
	   notification_email {					#指定keepalived故障时发送Email 
	     xxxxxx@xxx.com					#Email地址 
	    # failover@firewall.loc 
	    # sysadmin@firewall.loc 
	   } 
	   notification_email_from chaichuan@139.com					#发送邮箱 
	   smtp_server 127.0.0.1					#SMTP服务器，本地开启sendmail无需改动配置 
	   smtp_connect_timeout 30 
	   router_id LVS_DEVEL					#运行keepalived机器的一个标识 
	}

	vrrp_instance VI_1 {					#VRRP实例VI_1 
	    state MASTER					#初始值MASTER/SLAVE
	    interface eth0					#所绑定的网卡 
	    virtual_router_id 51					#VRID标记（0～255） 
	    priority 100					#优先级 
	    advert_int 1					#检查间隔
	    #nopreempt					#不抢占
	    authentication {					#认证设置，只能在state为backup，且优先级高的主机上 
	        auth_type PASS					#认证方式PASS（另外一种HA） 
	        auth_pass 1111 
	    } 
	    virtual_ipaddress {					#虚拟ip 
	        192.168.68.222 
	        
	    } 
	}
	virtual_server 192.168.68.222 80 {					#虚拟主机 
	    delay_loop 6 
	    lb_algo wrr					#LVS调度算法（rr|wrr|lc|wlc|lblc|sh|dh） 
	    lb_kind DR					#LVS群集模式（NAT|DR|TUN） 
	   # nat_mask 255.255.255.0   					#mask 
	    persistence_timeout 50					#会话保持时间 
	    protocol TCP					#使用协议

	    real_server 192.168.68.225 80 {					#真实服务器 
	        weight 10					#默认为1，0为不工作
	      	inhibit_on_failure          #服务器健康失败，将weight设置为0，而不删除
	      # notify_up    #检测到服务器开启执行的脚本
	      # notify_down    #检查服务器宕机执行的脚本


	      # SSL_GET {      #SSL检查方式 
	      #     url { 
	      #        path / 
	      #        digest ff20ad2481f97b1754ef3e12ecd3a9cc 
	      #      } 
	      #     url {     
	      #        path /mrtg/ 
	      #        digest 9b3a0c85a887a256d6939da88aabd8cd 
	      #      } 
	      #      connect_timeout 13 
	      #      nb_get_retry 3 
	      #      delay_before_retry 3 
	      #  }


	        TCP_CHECK{					#TCP方式健康检测
	               connect_timeout 4
            	   nb_get_retry 3
                   delay_before_retry 3
	         } 
	    }
	    real_server 192.168.68.226 80 {
	        weight 10
	      	inhibit_on_failure
	      # notify_up
	      # notify_down


	      # SSL_GET {
	      #     url { 
	      #        path / 
	      #        digest ff20ad2481f97b1754ef3e12ecd3a9cc 
	      #      } 
	      #     url {     
	      #        path /mrtg/ 
	      #        digest 9b3a0c85a887a256d6939da88aabd8cd 
	      #      } 
	      #      connect_timeout 13 
	      #      nb_get_retry 3 
	      #      delay_before_retry 3 
	      #  }


	        TCP_CHECK{
	               connect_timeout 4
            	   nb_get_retry 3
                   delay_before_retry 3
	         } 
	    }
	}