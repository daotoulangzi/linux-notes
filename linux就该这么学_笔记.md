#### 7、iptables
---
##### 7.2、基本命令参数
常见的控制类型：
- ACCEPT：允许通过, 
- LOG：记录日志，传给下一条继续匹配, 
- REJECT：拒绝通过，必要时给出提示, 
- DROP：直接丢弃，不给回应

基本参数
	
	-P 设置默认策略， iptables -P INPUT (DROP|ACCEPT)
	-F 清空规则链
	-L 查看规则链
	-A 在规则链末尾加入新规则
	-I num 规则链头部加入新规则
	-D num 删除某一条规则
	-s 匹配来源地址IP/MASK加叹号！表示除这个IP外。
	-d 匹配目标地址
	-i 网卡名称
	-o 网卡名称
	-p 匹配协议，如tcp,udp,icmp
	--dport num 匹配目标端口号
	--sport num 匹配来源端口号
	
- 查看已有规则：iptables -L
- 清空已有规则：iptables -F
- 将INPUT链的默认策略设置为拒绝：iptables -P INPUT DROP
- 允许所有的ping操作：iptables -I INPUT -p icmp -j ACCEPT
- 允许所有未被其他规则匹配上的数据包：iptables -t filter -A INPUT -j ACCEPT
- 仅允许来自192.168.10.0/24域的用户连接本机的ssh服务：iptables -I INPIT -s 192.168.10.0/24 -p tcp --dport 22 -j ACCEPT; iptables -A INPUT -p tcp --dport 22 -j REJECT
- 不允许任何用户访问本机的12345端口：iptables -I INPUT -p tcp --dport 12345 -j REJECT; iptables -I INPUT -p udp --dport 12345 -j REJECT
- 拒绝其他用户从 'eno16777736'网卡访问本机http服务的数据包：iptables -I INPUT -i eno16777736 -p tcp --dport 80 -j REJECT
- 禁止用户访问 www.my133t.org：iptables -I FORWARD -d www.my133t.org -j DROP
- 禁止 IP 地址是 192.168.10.10 的用户上网：iptables -I FPRWARD -s 192.168.10.10 -j DROP

DNAT 的数据包转换过程

    希望互联网中客户机访问到内网 192.168.10.6 提供网站服务的主机，需要在网管上运行这条命令：iptables -t nat -A PREROUTING -i eno16777736 -d 61.240.149.149 -p tcp --dport 80 -j DNAT --to-destination 192.168.10.6
	
端口转发与流量均衡
- 将访问192.168.10.10主机的888端口的请求转发22端口：firewall-cmd --permanent --zone=public --add-forward-port=port=888:proto=tcp:toport=22:toaddr=192.168.10.10

##### 7.3、Firewalld 防火墙
---
区域概念与作用
- trusted 允许所有的数据包
- todo


---
#### 8、使用ssh服务管理远程主机
---

##### 8.1、管理系统服务命令
|Sysvinit命令	|systemctl命令	|作用	|
|-|-|-|
|service foo start	|systemctl start foo.service	|启动服务	|
|service foo restart	|systemctl restart foo.service	|重启服务	|
|service foo stop	|systemctl stop foo.service	|停止服务	|	
|service foo reload	|systemctl reload foo.service	|重新加载配置文件	|
|service foo status	|systemctl status foo.service	|查看服务状态	|

监视资源与管理进程
- R：运行，正在运行或在运行队列中等待
- S：中断，休眠中，在等待某个条件的形成或接受信号
- D：不可中断，收到信号不唤醒和不可运行，进程必须等待直到有中断发生
- Z：僵死，进程已终止，但进程描述符存在，直到父进程调用wait4()系统系统调用后释放
- T：停止，进程收到SIGSTOP, SIGSTP, SIGTIN, SIGTOU信号停止运行

ps 命令用于查看系统中的进程状态
- ps -aux | grep **

top 命令用于监视进程的活动与系统负载，格式为：top

进程信息区中包含了各个进程的详细信息，含义如下：
- PID：进程 ID 号
- USER：进程所有者
- PR：优先级
- NI：优先级，负值表示优先级更高
- VIRT：虚拟内存使用量
- RES：物理内存使用量
- SHR：共享内存大小
- S：进程状态
- %CPU：CPU使用百分比
- %MEM：内存的使用百分比
- TIME+：使用CPU的时间
- COMMAND：命令名称

pidof 查询某个特定程序的进程PID值：pidof **

kill 命令用于终止某个特定的PID码进程：kill **

killall 命令用于终止某个特定名称的所有进程，格式为： killall **

Ctrl+c 终止进程；Ctrl+z 暂停进程。
- jobs：查看后台运行的进程
- bg %n：让进程n到后台去
- fg %n：让进程n到前台来
(使用 Ctrl+z 暂停当前命令并放到后台，使用 jobs 可以查看后台运行的进程；bg 将后台暂停的命令变成继续执行；fg 将后台中的命令调至前台继续运行)

##### 8.2、查看网卡信息
nmcli：一款能够方便我们配置网络的工具，能够轻松的查看网卡信息或网络状态
- 查看网卡配置信息：nmcli connection show
- 查看网卡的连接状态：nmcli device status
- 查看网卡设备的详细信息：nmcli con show **

ss 查看端口连接状态，具体的参数：

	-a 显示所有的套接字
	-l 显示所有连接状态的套接字
	-e 显示详细的套接字信息
	-m 显示套接字的内存使用情况
	-p 显示套接字的进程信息
	-4 显示 ipv4 的套接字信息
	-6 显示 ipv6 的套接字信息
	-t 仅显示 tcp 的套接字信息
	-u 仅显示 udp 的套接字信息
	-n 不解析主机名
	-s 查看概述





