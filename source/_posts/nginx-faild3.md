---
title: nginx_faild3
date: 2019-01-26 17:49:09
tags: nginx
---

**nginx有的时候启动会报错nginx: [alert] kill(1022, 1) failed (3: No such process)问题的解决:**

首先出现这种报错时候,正常的启动nginx依然会报这种错误,所以可以用 -c 方式指定nginx配置文件来启动从而解决该问题,格式如下:

启动代码格式：nginx安装目录地址 -c nginx配置文件地址

	```
	[root@192 sbin]# /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf	
	```
