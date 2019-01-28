---
title: vm_share
date: 2019-01-28 14:58:08
tags: vm
---

1. **命令行安装Vmware Tools 本机虚拟机共享文件,在VMware点击VM,在单击"install VMware Tools..."**

2. **输入ls /dev查看**	

	```
	ls /dev
	```

3. **mkdir /mnt/cdrom 在/mnt目录下新建一个名为cdrom的文件夹**	

	```
	mkdir /mnt/cdrom
	```

4. **mount -t iso9660 /dev/cdrom /mnt/cdrom 将光盘挂载到/mnt/cdrom目录下**	
	
	```
	mount -t iso9660 /dev/cdrom /mnt/cdrom
	```

5. **ls /mnt/cdrom/查看内容，再执行 cp /mnt/cdrom/VMwareTools-9.2.0-799703.tar.gz /root/vm.tar.gz,将名为“VMwareTools-9.2.0-799703.tar.gz”复制到/root目录下，并重新命名为vm.tar.gz**	

	```
	ls /mnt/cdrom/
	cp /mnt/cdrom/VMwareTools-9.2.0-799703.tar.gz /root/vm.tar.gz
	```


6. **然后解压第5步里的文件tar -xzf vm.tar.gz**	

	```
	tar -zxvf vm.tar.gz
	```

7. **cd vmware-tools-distrib/，输入./vmware-install.pl安装，若出现错误-bash: ./vmware-install.pl: /usr/bin/per: bad interpreter: No such file or directory，表明未安装编译环境**

	```
	./vmware-install.pl
	```

8. **若第7步报错,则安装编译环境 yum -y install perl gcc make kernel-headers kernel-devel**

	```
	yum -y install perl gcc make kernel-headers kernel-devel
	```

9. **第八步编译安装成功,再重新安装./vmware-install.pl**
	
	```
	./vmware-install.pl
	```

10. **VMware Tools已经安装完毕，提示可以运行/usr/bin/vmware-uninstall-tools.pl命令卸载VMware Tools。第一次运行时需运行/usr/bin/vmware-config-tools.pl命令配置VMware Tools，按回车键直接运行。需要注意的是,如果卸载命令不起作用,可执行rpm -e open-vm-tools-desktop 卸载并且重新安装**

11. **以上都执行完毕可能会出现问题,报错如下(含解决方法)**

	Do you want to enable Guest Authentication (vgauth)? [yes] 
	No X install found.
	Creating a new initrd boot image for the kernel.
	vmware-tools-thinprint start/running
	initctl: Job failed to start 
	Unable to start services for VMware Tools

	**要把这个conf文件删掉**

	```
	rm /etc/init/vmware-tools-thinprint.conf
	```

	**再重启vmware-tools的服务即可**

	```
	/etc/vmware-tools/services.sh start
	```

	**如果在这之后出现 blocking file system failed 报错信息,则yum install fuse-libs 命令来解决**

	```
	yum install fuse-libs 
	```

	**如果在这之后出现 Mounting HGFS shares: FAILED. 报错信息,因为虚拟机设置中没有启用共享文件夹功能,启用就好**