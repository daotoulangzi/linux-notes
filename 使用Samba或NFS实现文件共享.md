#### 11、使用 Samba 或 NFS 实现文件共享
---
> nfs = Network File System，不支持 Linux 与 Windows 系统间的文件共享

早期网络在不同主机之间共享文件大多使用 FTP 协议来传输，但 FTP 协议仅能做到传输文件却不能直接修改对方主机的资料数据；于是出现 NFS 开源文件程序,属于轻量级的文件共享服务，不支持 Linux 与 Windows 系统间的文件共享。

##### 11.3、NFS网络文件系统
> NFS 文件系统协议允许网络中的主机通过 TCP/IP 协议进行资源共享。NSF 服务依赖于 RPC 服务与外部通信，必须保证 RPC 服务能够正常注册服务的端口信息才能正常使用 NFS 服务

安装：yum install nfs-utils

- 1、创建 NFS 服务端的共享目录：
	- 清空默认规则链：iptables -F
	- 保留清空后的 iptables 规则：service iptables save
	- 创建 nfsfile 共享目录：mkdir /nfsfile
	- nfs 服务端配置文件 /etc/exports，用于要定义的共享目录以及相应权限：vim /etc/exports; (//格式为：共享目录的绝对路径 允许访问NFS资源的客户端（权限参数）) /nfsfile 192.168.10.* (rw,sync,root_squash)

	|参数	|作用	|
	|-	|-	|
	|ro	|只读默认	|
	|rw	|读写模式	|
	|root_squash	|当NFS客户端使用root用户访问时，映射为NFS服务端的匿名用户	|
	|no_root_squash	|当NFS客户端使用root用户访问时，映射为NFS服务端的root用户|
	|all_sqash	|无论NFS客户端使用任何账户，均映射为NFS服务端的匿名用户	|
	|sync	|同时将数据写入到内存和硬盘中，保证不丢失数据	|
	|async	|优先将数据保存到内存中，然后写入硬盘，效率更高，但可能造成数据丢失	|

- 2、启动 NFS 服务（注意NFS服务是依赖于RPC服务的）
	- systemctl status rpcbind
	- 启动nfs-server程序：systemctl start nfs-server
	- 设置为开机启动：systemctl enable nfs-server
	
- 3、配置 nfs 客户端
	- showmount 命令用于查询NFS服务端共享信息，格式：showmount [参数] [远程主机]
	
	|参数	|作用	|
	|-	|-	|
	|-e	|显示NFS服务端的共享列表	|
	|-a	|显示本机挂载NFS资源的情况	|
	|-v	|显示版本号	|
	
	- 查询远程NFS服务端中可用的共享资源：showmount -e 192.168.10.10
	- 创建本地挂在目录：mkdir /nfsfile; mount -t nfs 192.168.10.10:/nfsfile /nfsfile
	- 开机自动将 NFS 资源挂载到本地，vim /etc/fstab; 192.168.10.10:/nfsfile /nfsfile nfs defaults 0 0
	
AutoFs 自动挂载服务
	
	AutoFs 服务与 Mount/Umount 命令不同之处在于他是一种守护进程，只有检测到用户试图访问尚未挂载的文件系统时才会自动检测并挂载改文件系统；
	将挂载信息填入 /etc/fstab 文件后系统将在每次开机时都自动将其挂载，而运行 AutoFs 后则是当用户需要使用该文件系统了才会动态的挂载，节约网络与系统资源。
	
- 1、启动 autofs 服务并加入到开机启动项
	 - systemctl start autofs
	 - systemctl enable autofs
- 2、修改 autofs 主配置文件（格式为：挂在目录 映射配置文件）
	- vim /etc/auto.master; /media /etc/iso.misc
- 3、编辑挂载配置参数文件
	- vim /etc/auto.misc; iso -stype=iso9660,ro,nosuid,nodev :/dev/cdrom
	- 重启 autofs 服务：systemctl restart autofs
	
##### 11.2、samba
- TODO
	



