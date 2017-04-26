---
layout: post
title:  "jcmd、jstack、jmap抓包"
date:   2017-4-5 14:38:28    
categories: jekyll update
---

jcmd是从JDK 7开始引入的一个JVM诊断命令行工具，可以向运行中的JVM发送诊断命令

 在保证了JDK版本的前提下，欲使Linux上下载下来的jfr文件能在本地运行，还需要在搭有Linux环境的服务器JVM中添加如下参数：

	-XX:+UnlockCommercialFeatures
	-XX:+FlightRecorder

resin

	<jvm-arg>-XX:+UnlockCommercialFeatures</jvm-arg>
	<jvm-arg>-XX:+FlightRecorder</jvm-arg>

然后在Linux环境上运行如下命令生成jfr文件：

	su www -lc "/usr/java/jdk1.7.0_40/bin/jcmd ${ppid} JFR.start name=test duration=60s settings=default.jfc filename=output_`date +%m%d%H%M`.jfr"

待文件生成成功后，下载相应的jfr双击即可查看相关信息。(代码->热点方法->堆栈跟踪)

使用到的其它命令如下：

      jcmd                           ----查看JVM进程的PID
      jcmd -l                        
      jcmd pid Thread.print   ----打印堆栈信息，功能类似于jstack -m pid

其他：

jstack线程：（632是进程ID）

	su www -lc "/usr/java/jdk1.7.0_40/bin/jstack 632" > /home/www/jstack_out.txt

jmap内存：

	su www -lc "jmap -F -dump:format=b,file=jmap.dat 6214"