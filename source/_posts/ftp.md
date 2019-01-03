---
title: ftp
date: 2019-01-02 21:26:22
tags: ftp
---
	
1. **首先查看是否有安装vsftpd**
	```
	rpm -qa | grep vsftpd	
	```

   **没有就安装并开机启动**
	```
	yum -y install vsftpd
	chkconfig vsftpd on
	```

2. **因为ftp用户不必每个都是系统的真实用户所以采用虚拟用户配置ftp**
   **修改配置文件,打开/etc/vsftpd/vsftpd.conf,配置如下:**
	```
	anonymous_enable=NO //设定不允许匿名访问
	local_enable=YES //设定本地用户可以访问。注：如使用虚拟宿主用户，在该项目设定为NO的情况下所有虚拟用户将无法访问
	chroot_list_enable=YES //使用户不能离开主目录
	ascii_upload_enable=YES
	ascii_download_enable=YES //设定支持ASCII模式的上传和下载功能
	pam_service_name=vsftpd //PAM认证文件名。PAM将根据/etc/pam.d/vsftpd进行认证
	```
   **下面设置是vsftpd虚拟用户重要配置**
	```
	guest_enable=YES //设定启用虚拟用户功能(手动添加,默认不包含这些设定)
	guest_username=ftp //指定虚拟用户的宿主用户，CentOS中已经有内置的ftp用户了(手动添加,默认不包含这些设定)
	user_config_dir=/etc/vsftpd/vuser_conf //设定虚拟用户个人vsftp的CentOS FTP服务文件存放路径。存放虚拟用户个性的CentOS FTP服务文件(配置文件名=虚拟用户名)(手动添加,默认不包含这些设定)
	```
 
3. **认证**
   **安装Berkeley DB包**
	```
	yum install db4 db4-utils

	```

   **再创建用户信息文件/etc/vsftpd/vuser_passwd.txt,奇数行用户名,偶数行密码**
	```
	curry
	123456
	curry30
	wzx123
	```

   **生成虚拟用户认证的db文件(此步骤很关键,以后每次在/etc/vsftpd/vuser_passwd.txt新配置一个用户,都有再生成一遍db文件)**
	```
	db_load -T -t hash -f /etc/vsftpd/vuser_passwd.txt /etc/vsftpd/vuser_passwd.db
	```

   **随后，编辑认证文件/etc/pam.d/vsftpd，全部注释掉原来语句，新增下面配置**
	```
	auth required pam_userdb.so db=/etc/vsftpd/vuser_passwd
	account required pam_userdb.so db=/etc/vsftpd/vuser_passwd
	```

   **最后，创建虚拟用户配置文件**
	```
	mkdir /etc/vsftpd/vuser_conf/
	vi /etc/vsftpd/vuser_conf/curry30  //文件名等于vuser_passwd.txt里面的账户名，否则下面设置无效
	```

   **内容如下,要注意所有配置的文件都不要有空格,不然怎么都会连不上的。。。而且找不到原因，那是相当蛋疼的。。。**
	```
	local_root=/home
	write_enable=YES
	anon_umask=022
	anon_world_readable_only=NO
	anon_upload_enable=YES
	anon_mkdir_write_enable=YES
	anon_other_write_enable=YES
	```

   **设置Selinux**
	```
	setsebool -P ftp_home_dir=1   //设置ftp可以使用home目录
	sersebool -P allow_ftpd_full_access=1   //设置ftp用户可以有所有权限
	```

   **设置curry30根目录权限**
	```
	chmod R 777 /home //因为我的网站根目录在home下
	```

   **有可能进行防火墙的设置 打开21端口 vim/etc/sysconfig/iptables**
	```
	-A INPUT -m state --state NEW -m tcp -p -dport 21 -j ACCEPT
	```

   **重启防火墙**
	```
	service iptables restart
	```

   **重启vsftpd生效**
	```
	service vsftpd restart	
	```