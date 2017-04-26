---
layout: post
title:  "html frame框架简单使用"
date:   2017-1-16 16:59:25 
categories: jekyll update
---

首先撰写index.html,也就是默认页

	root@pts/0 # cat index.html 
	<html>
	<frameset cols="120,*">
	<frame name="daohang" src="daohang.html" />
	<frame name="content" src="front.pdf" />
	</frameset>
	</html>
	
再次写导航里的页面，也就是左边框的定义

	root@pts/0 # cat daohang.html 
	<html>
	<head>
	</head>
	<body>
	<style>
	a:link { 
	color:#FF0000; 
	text-decoration:underline; 
	} 
	a:visited { 
	color:#00FF00; 
	text-decoration:none; 
	} 
	a:hover { 
	color:#000000; 
	text-decoration:none; 
	} 
	a:active { 
	color:#FFFFFF; 
	text-decoration:none; 
	}
	.font_01 { font-size:8px;}
	</style>
	 <p><a href="front.pdf" target="content" class="font_01">前台系统架构图 </a></p>
	 <p><a href="baby.pdf" target="content" class="font_01">baby计划架构图</a></p>
	  <p><a href="db.pdf" target="content" class="font_01">数据库架构图</a></p>
	 <p><a href="apollo.pdf" target="content" class="font_01">apollo架构图</a></p>
	</body>
	</html>

如下：

![](http://7xppz2.com1.z0.glb.clouddn.com/92.png)