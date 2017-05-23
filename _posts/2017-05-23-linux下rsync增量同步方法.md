---
layout: post
title:  "linux下rsync增量同步方法"
date:   2017-5-23 19:43:13 
categories: jekyll update
---

可以先使用rpm －qa |grep rsync 查看rsync是否已经安装

 

下面说说rsyns的配置过程

一. 配置服务器端

首先编辑 /etc/rsyncd.conf 内容如下:

uid = nobody #进行备份的用户nobody为任何用户

gid = nobody #进行备份的组 nobody为任何组

use chroot = no #不使用chroot

max connections = 10 #最大连接数

log file = /var/log/rsyncd.log #日志文件

 

[rsyncd] # 这里是认证的模块名

path = /home/test/ #参与同步的目录

ignore errors # 可以忽略一些无关的IO错误

read only = yes #只读

list = no #不允许列清单

anth users = root #认证的用户名

secrets file = /etc/rsyncd.secrets #密码文件存放地址

 

然后在/etc下面编辑一个rsyncd.secrets的密码存放文件

格式为

用户名:密码如 root:xl1100

 

最后在server端将rsync以守护进程形式启动

命令为: rsync –daemon

rsync启动的端口为 873端口

 

 

二. 客户端的配置

在/etc下面编辑一个rsyncd.secrets的密码存放文件即可

 

执行命令为:

rsync -vzurtopg --progress --deleteroot@192.168.100.150::rsyncd /home/test --password-file=/etc/rsync.secret

参数 v 表示详细提示

z 表示压缩

u 表示只进行更新

topg 保持文件原有属性如属主、时间的参数

--progress 指显示

--delete 指如果服务器端删除了这一文件，那么客户端也相应把文件删除，保持真正的一致

root 为用户名

@192.168.100.150为服务器IP

::后面的rsyncd为认证的模块名

/home/test 为备份到本地的目录名

--password-file=/etc/rsync.secret 制定密码存放位置的地址

 

 

可以编写脚本 rsync.sh

#!/bin/sh

rsync -vzrtopg --progress --deleteroot@192.168.100.150::rsyncd /home/test --password-file=/etc/rsync.secret

 

然后将该脚本加入到/etc/crontab文件中定时执行更新