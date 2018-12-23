---
title: ratchet
date: 2018-12-23 16:11:36
tags: websockets
---


1. **首先在本地搭建好chat_client,chat_server, chat_website**

2. **然后因为mysql5.7以上date类型不再支持0000-00-00的格式,所以在执行导入sql文件后,进入msql控制台,执行如下语句**
	```
	set GLOBAL sql_mode ='';
	```

3. **为了方便调试可以在chat_website上进行相应的调试
	如果本地测试需要直接回复信息,可去掉发信息的条件限制,在
	chatserver/app/models/ChatUser.php的canSendMessage方法中直接返回**
	```
	return true;
	```
	

4.	**在chat_website上本地测试**
	
	(1)更改chat_website/index.php 约131行:

	```
	echo "<script>location.href = '/';</script>"
	```

	(2)注释chat_website/index.php 约65,66行 并且复制一条新的chatJsUrl 更换成自己本地搭建chatclient的虚拟域名地址如下:

	```
	var chatJsUrl = "http://chatsmall.com/js/"; //curry
	```

	(3)更改chat_client端代码chat_small/js/client.chat.main.js 18,19行注释掉 并去掉21,22行的注释,也就是将serverclient的本机虚拟域名替换,并且为了纠正路径问题,要去掉该文件中所有的chat_client路径
	
	```
	 var chatIP = "chatsmall.com"; // Test
	```
	```
	 var chatUrl = "http://" + chatIP; // Test
	```

	(4)更改chat_client端代码chat_small/index.php
	并且修改242行的chatIP为自己本机ip
	并且修改244行apiUrl为自己本地的chatserver的虚拟域名
	
	```
	var chatIP = "127.0.0.1"; // Test
	```

	```
	var apiUrl = "http://chatserver.com"; // Test
	```

	(5)更改chat_client端代码chat_small/js/scripts.js的115行注释掉 并且取消116行的注释

	```
	//webSocket = new WebSocket("wss://" + chatIP + "/wss"); // Live
	```

	```
	webSocket = new WebSocket("ws://" + chatIP + ":" + port); // Test
	```
		
		
	
 
 





