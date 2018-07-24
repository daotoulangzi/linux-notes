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





