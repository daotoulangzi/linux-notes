#### 12、使用 Bind 提供域名解析服务
> 了解 DNS 服务程序的原理，学习正向解析与反向解析

服务器形式：
- 主服务器：特定区域内具有唯一性，负责维护区域内的域名与 IP 地址对应关系
- 从服务器：从主服务器获取域名与 IP 地址对应关系并维护，以防止服务器宕机情况
- 缓存服务器：提高重复查询时的效率

查询方式：
- 递归查询：用于客户机向 DNS 服务器查询
- 迭代查询：用于 DNS 服务器向其他 DNS 服务器查询

服务程序的配置文件如下：

|主程序  |/usr/sbin/named  |
|-  |-  |
|主配置文件  |/etc/named.conf  |
|区域配置文件 |/etc/named.rfc1912.zones |

当用户访问一个域名时，正常情况会向指定的 DNS 主机发递归查询请求，如果 DNS 主机中没有改域名的解析信息，那么会不断向上级 DNS 主机进行迭代查询；

根 DNS 服务器 IP 地址文件：`/var/named/named.ca`

DNS 服务的解析实验：
- 主配置文件：`/etc/named.conf`
- 区域信息文件：`/etc/named.rfc1912.zones`，用于定义域名与 IP 地址解析规则保存的文件位置以及区域服务类型等内容；
- 区域数据文件：`/var/named/**`

服务类型：
- hint：根区域
- master：主区域
- slave：辅助区域

named-checkconf 或 named-checkzone 命令来分别用于检查主配置与区域数据文件中语法或参数的错误

正向解析实验：
- 1、配置区域数据信息：vim /etc/named.rfc1912.zones
 ```
zone "linuxprobe.com" IN {
type master;
file "linuxprobe.com.zone";
allow-update {none;};
}
 ```
 - 2、配置解析数据信息
 ```
 直接复制正向解析模板文件："/var/named/named.localhost"，填写信息后即可直接使用。
 cd /var/named/
 cp -a named.localhost linuxprobe.com.zone
 vim linuxprobe.com.zone
 systemctl restart named
 ```
 
 查看服务器的 NDS 信息：cat /etc/resolv.conf; nslookup 127.0.0.1 | grep Server; dig
 
 FQDN：Full Qualified Domain Name 也叫主机名，如 www.baidu.com 是主机名，baidu.com 和 .com 是域名。
 
 NDS 资源记录类型：
 ```
 数据库中每一个条目称为资源记录（Resource Record, RR）,资源记录的格式：
 ```
 
 |NAME  |[TTL]  |IN(关键字)  |RRT(资源记录类型)  |VALUE  |
 |- |-  |-  |-  |-  |
 |www.hulunbeier.com. | |IN |A  |172.16.0.1 |
 |172.16.0.1  | |IN |PTR  |www.hulunbeier.com |
 
 - 资源记录类型
 ```
 SOA(Start OF Authority)：起始授权记录（第一条必须是此记录）
  ZONE NAME    TTL   IN    SOA   FQDN    ADMINISTRATOR_MAILBOX(
                    serial num: 版本号
                    refresh: 检查时间周期
                    retry: 充实时间
                    expire: 过期时间
                    nagative answer TTL: 否定答案缓存时间)
  时间格式：M分钟, H小时, D天, W周, 默认秒
  邮箱格式：admin@hulunbeier.com --写为-- admin.hulunbeier.com
  
  如：
  hulunbeier.com.   500  IN   SOA   ns1.hulunbeier.com.   admin.hulunbeier.com. (
                      2013040101
                      1H
                      5M
                      1W
                      1D)
 ```
 ```
 NS(Name Server): DOMAIN NAME --> FQDN 名称服务器(任何一个NS记录都有一个A记录与其对应)
 如：
 hulunbeier.com.    500   IN   NS   ns1.hulunbeier.com.
 ns1.hulunbeier.com.    500   IN    A   172.16.0.1
 ```
 ```
 MX(Mail exchange): ZONE NAME --> FQDN 邮件交换器
 ZONE NAME    TTL   IN    MX    PRI   VALUE
 优先级：0-99，数字越小，级别越高
 如：
 hulunbeier.com.   500   IN    MX    10    mail.hulunbeier.com.
 mail.hulunbeier.com.   500   IN    A   172.16.0.10
 ```
 ```
 A(address): FQDN --> ipv4
 ```
 ```
 AAAA: FQDN --> ipv6
 ```
 ```
 PTR(pointer): IP --> FQDN 指针记录，用于反向解析
 ```
 ```
 CNAME(Canonical NAME): Alias --> FQDN 别名记录
 www2.hulunbeier.com.   IN    CNAME   www.hulunbeier.com.
 ftp    IN    CANME   www
 ```
 
 区域文件，包括正向区域文件和反向区域文件：
 ```
 正向区域文件格式：
    hulunbeier.com.    IN    NS    www.hulunbeier.com.
    www.hulunbeier.com.    IN    A   192.168.0.1
    第二行可简写为：
    www    IN    A   192.168.0.1
    
 反向区域文件
    0.168.192.in-arpa.    IN    SOA   FQDN
    如：
    1.0.168.192.in-arpa.    IN    PTR   www.hulunbeier.com.
    可简写为：
    1   IN    PTR   www.hulunbeier.com.
 ```
 
 
 
