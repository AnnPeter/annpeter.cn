---
layout: post
title: "CentOS7安装Oracle12c"
date: 2016-03-20 22:10:15 +5325
comments: true
categories: Server
keywords: Server,CentOS,Linux,博客,AnnPeter's Blog
description: "CentOS7安装Oracle12c"

---
首先请参考教程，卸载OpenJDK，安装JDK。

1. 修改hostname，这两个文件中的地址必须保持一致，根据自己的情况修改，可以使用内网地址
	> $ /etc/hosts 
		10.144.140.88 CentOS
	> $ /etc/sysconfig/network
		HOSTNAME=10.144.140.88

<!-- more -->

2. 关闭selinux
	> $ /etc/selinux/config
		设置SELINUX=disabled

3. 关闭防火墙
	> $ systemctl start firewalld.service\*

4. 安装依赖包
	> $ yum -y install binutils compat-libcap1  compat-libstdc++-33 compat-libstdc++-33*.i686 elfutils-libelf-devel gcc gcc-c++ glibc*.i686 glibc glibc-devel glibc-devel*.i686 ksh libgcc*.i686 libgcc libstdc++ libstdc++*.i686 libstdc++-devel libstdc++-devel*.i686 libaio libaio*.i686 libaio-devel libaio-devel*.i686 make sysstat unixODBC unixODBC*.i686 unixODBC-devel unixODBC-devel*.i686 libXp unzip

5. 下载Oracle12c至/tmp目录，解压。

6. 添加安装用户和用户组
	> $ groupadd oinstall
	> $ groupadd dba
	> $ useradd -g oinstall -G dba oracle
	> $ passwd oracle

7. 添加安装目录
	> $ mkdir -p  /usr/local/oracle/product/12.1.0/dbhome\_1
	> $ chown -R oracle:oinstall /usr/local/oracle
	> $ chmod -R 755 /usr/local/oracle

8. 修改内核参数配置文件
	> fs.aio-max-nr = 1048576
	> fs.file-max = 6815744
	> kernel.shmall = 2097152
	> kernel.shmmax = 1200000000
	> kernel.shmmni = 4096
	> kernel.sem = 250 32000 100 128
	> net.ipv4.ip\_local\_port\_range = 9000 65500
	> net.core.rmem\_default = 262144
	> net.core.rmem\_max = 4194304
	> net.core.wmem\_default = 262144
	> net.core.wmem\_max = 1048576

	> $ sysctl –p //显示内核参数配置信息

9. 修改用户限制文件
	> oracle              soft    nproc   2047
	> oracle              hard    nproc   16384
	> oracle              soft    nofile  1024
	> oracle              hard    nofile  65536
	> oracle              soft    stack   10240

10. 修改文件/etc/pam.d/login 
	在最后一个大项前面加入session    required     pam\_limits.so

11. 修改文件/etc/profile，添加一下内容
	>   if [ $USER = "oracle" ]; then
	> 	 if [ $SHELL = "/bin/ksh" ]; then
	> 	      ulimit -p 16384
	> 	      ulimit -n 65536
	> 	  else
	> 	      ulimit -u 16384 -n 65536
	> 	  fi
	>   fi

12. 切换到oracle用户

13. 设置oracle用户的环境变量/home/oracle/.bash\_profile
	TMP=/tmp; export TMP
	TMPDIR=$TMP; export TMPDIR
	ORACLE\_HOSTNAME=10.144.140.88; export ORACLE\_HOSTNAME
	ORACLE\_BASE=/usr/local/oracle; export ORACLE\_BASE
	ORACLE\_HOME=$ORACLE\_BASE/product/12.1.0/dbhome\_1; export ORACLE\_HOME
	ORACLE\_SID=orcl; export ORACLE\_SID
	PATH=$ORACLE\_HOME/bin:/usr/sbin:$PATH; export PATH

14. 编辑静默安装响应文件
	> $ cp -R /tmp/database/response/  .
	> $ cd response
	> $ vim db\_install.rsp

	需要设置的选项如下：  
	oracle.install.option=INSTALL\_DB\_SWONLY
	ORACLE\_HOSTNAME=10.144.140.88
	UNIX\_GROUP\_NAME=oinstall
	INVENTORY\_LOCATION=/usr/local/oracle/inventory
	SELECTED\_LANGUAGES=en,zh\_CN
	ORACLE\_HOME=/usr/local/oracle/product/12.1.0/dbhome\_1
	ORACLE\_BASE=/usr/local/oracle
	oracle.install.db.InstallEdition=EE
	oracle.install.db.DBA\_GROUP=dba
	oracle.install.db.OPER\_GROUP=dba
	oracle.install.db.BACKUPDBA\_GROUP=dba
	oracle.install.db.DGDBA\_GROUP=dba
	oracle.install.db.KMDBA\_GROUP=dba
	DECLINE\_SECURITY\_UPDATES=true

15. 添加swap空间，如果你有交换空间的话可以跳过这个步骤
	dd if=/dev/zero of=/home/swapfile bs=1M count=512
	mkswap /home/swapfile
	swapon /home/swapfile

	然后修改 /etc/fstab，加上：
	/home/swapfile swap swap defaults 0 0 

15. 进入安装包目录/tmp/database/，执行安装
	> $ ./runInstaller -silent -noconfig -responseFile /home/oracle/response/db\_install.rsp

16. 可以通过top查看安装进程
	可以通过tail -f /usr/local/oracle/oraInventory/logs/installActions2016-03-12\_04-40-06AM.log ，查看安装日志。

17. 安装完成，切换到root身份
	> $ sh /usr/local/oracle/inventory/orainstRoot.sh 
	> $ sh /usr/local/oracle/product/12.1.0/root.sh 

18. 重新登入oracle用户，以静默方式配置监听。如果未找到netca，请检查环境变量。 
	> $ netca  /silent /responsefile /home/oracle/response/netca.rsp 

19. 通过netstat -nlpt，查看到端口1521正在被监听。

20. 以静默方式建立新库
