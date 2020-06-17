---
title: docker for windows
date: 
tags: docker
---

前提环境win7系统,linux下的安装就不说了,几条命令就ok了,相对比较麻烦的就是win下的环境,总的来说会出现以下集中情况:

第一种情况:

首先是提示default Boot2Docker ISO is out-of-date,主要告诉我们在C:\Users\jr\.docker\machine\cache(jr是我本机名字,实际情况已自己为准)下的boot2docker.iso文件不是最新的,所以会去下载最新的iso文件,但是网上有的说可以先关闭网络然后再安装,就不会检测版本,个人建议还是不要这样做,后期因为不是最新的有可能虚拟机出问题,到时候导致docker出现问题,所以直接去github下载最新的iso即可,所有的版本在这里(最好自己有vpn)

https://github.com/boot2docker/boot2docker/releases

第二种情况:

如下所示,当走过第一个坑的时候,第二个坑又来了,启动docker terminal的时候,就会出现下面问题,总之就是告诉你当前你自己用的这台电脑没有开启cpu虚拟机化,so直接重启机器进入BIOS,主要找到
config > virtualization Technology > 设置成Enabled然后重启机器就全部完事了

```

Error creating machine : Error in driver during machine creation. This computer doesn't have VT-X/AMD enabled. Enabling it in the BIOS is mandatory

```



