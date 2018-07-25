#### 8、使用ssh服务管理远程主机
---
##### 8.2、查看端口状态
监听状态中的套接字：ss -ntl
查看进程名和 PID 号码：ss -s
查看 IP 数据包从本机到另外一台电脑经过的路由信息：tracepath www.linuxprobe.com

##### 8.3、远程控制服务
ssh 服务的配置文件解析：cat /etc/ssh/sshd_config
- Port 22	默认的 sshd 服务端口
- LisitenAddress 0.0.0.0	sshd 服务监听的IP地址
- Protocol 22	ssh 协议版本号
- HostKey /etc/ssh/ssh_host_rsa_key	SSH 协议版本为2时，RSA 私钥存放的位置
- PermitRootLogin yes	 设置是否允许 root 用户直接登录
- StrictModes yes	当远程用户私钥改变时则直接拒绝连接
- MaxAuthTries 6	最大密码尝试次数
- MaxSessions 10	最大终端数
- PasswordAuthentication yes	是否允许密码验证
- PermitEmptyPasswords no	是否允许空密码登录

远程主机上启动 sshd 服务并加入到开机启动项
- systemctl start sshd
- systemctl enable sshd

安全密钥验证
- 1、ssh-keygen # 一直回车即可
- 2、ssh-copy-id 192.168.10.20	# 将生成好的公钥秘钥传送到远程主机
- 3、vim /etc/ssh/sshd_config

	PasswordAuthentication no	# 允许密码验证的参数设置为 no
	PubkeyAuthentication yes	# 允许秘钥验证的参数设置为 yes
- 4、systemctl restart sshd	# 重启 ssh 服务

scp 命令用于在网络中安全的传输文件

|参数	|	作用	|
|-	|-	|
|-v	|显示详细的连接进度		|
|-P	|指定远程主机的sshd端口号	|
|-r	|传输文件夹时请加此参数	|
|-6	|使用 ipv6 协议|
- scp -r results/ root@192.168.0.1:/tmp  # 将本机 results 文件夹传送到远程主机 /tmp 目录下
- scp root@192.168.0.1:/tmp/test.py .	# 将远程主机上的 /tmp/test.py 文件拷贝到本机

##### 8.4、不间断会话服务
screen

|参数	|作用	|
|-	|-	|
|-A	|让所有的视窗自动调整适应当前终端机的大小|
|-d <会话名称>	|将指定 screen 会话离线	|
|-r <会话名称>	|将指定 screen 会话恢复	|
|-h <行数>	|指定视窗的缓冲区行数	|
|-S <会话名称>	|指定 screen 会话的名称|
|-ls	|显示当前 screen 会话|
|-x	|恢复所有离线的会话|
|--wipe	|自动将无法使用的 screen 会话删除|
- screen -S backup	# 创建名称为 backup 的会话
- screen -ls	# 查看当前已经存在的会话
- screen -r backup	# 回到 backup 会话中
- screen -d backup	# 将会话离线

会话共享：
- 将两台 linux 主机均连入同一个服务器
- 在主机 A 的终端上执行创建会话的操作：screen -S linuxprobe
- 在主机 B 的终端上同步会话消息：screen -x
- 此时，终端 A 和终端 B 上做的任何操作都可以实时同步到对方的屏幕上。

