---
title: apache403
date: 2019-03-16 17:05:30
tags: apache
---

最近因为要玩docker因为之前的系统是centos 6.5 ，所以换了一个新的7.6 centos版本的, 然后发现centos7.6 安装vm-tools 和6.x 并

不一样,因为在apache用的ln-s 形式访问的站点(这里用了共享hgfs的文件)导致各种403, 起初一般都是vhost或者httpd.conf少了一些配置,但

是该加的都加了还是403,最后排除法我创建了一个dir就可以访问,ln就不行了,然后试着更改 /mnt/hgfs 但是发现被 not permission ,然后觉

得是这里问题,原来centos7 vmtools 启动时候要加allow_other 选项, 如果不加, 系统其它的用户就不能访问该文件夹里的问价,所以就会出现

我之前apache遇到的403 forbidden.................

所以在这里提醒大家只要是centos 7.x vmtools和windows 共享文件开发时候,一定要加如下,

	```
	sudo vmhgfs-fuse .host:/ /mnt/hgfs -o allow_other

	```

运行以上,如果之前开启没有加allow_other,就会hgfs访问不了 会提示如下

	```
	fuse: mountpoint is not empty
	fuse: if you are sure this is safe, use the 'nonempty' mount option

	```

然后直接加进去 nonempty

	```
	sudo vmhgfs-fuse .host:/ /mnt/hgfs -o nonempty -o allow_other

	```

最后来一句,就是403很多,大都很常见,这个情况比较少见...比较恶心



