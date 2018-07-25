#### 9、使用 Apache 服务部署静态网站
---
安装 Apache 服务程序：yum install httpd
运行 Apache 服务程序并设置为开机启动：systemctl start httpd; systemctl enable httpd

##### 9.4、配置服务文件参数

|服务目录 |/etc/httpd |
|配置文件 |/etc/httpd/conf/httpd.conf |
|网站数据目录 |/var/www/html|
|访问日志 |/var/log/httpd/access_log  |
|错误日志 |/var/log/httpd/error_log |

Apache 服务程序的配置文件 /etc/httpd/conf/httpd.conf

|ServerRoot |服务目录|
|ServerAdmin  |管理员邮箱|
|User |运行服务的用户  |
|Group  |运行服务的用户组 |
|ServerName |网站服务器的域名 |
|DocumentRoot |网站数据目录 |
|Listen |监听 IP 地址与端口号 |
|DirectoryIndex |默认的索引页面|
|ErrorLog |错误日志文件 |
|CustomLog  |访问日志文件 |
|Timeout  |网页超时时间，默认为 300 秒|
|Include  |需要加载的其他文件  |

##### 9.5、强制访问控制安全子系统
SELinux = Security-Enhanced Linux
- enforcing：安全策略强制启用模式，将会拦截服务的不合法请求
- permissive：遇到服务越权访问只会发出警告而不强制拦截
- disabled：对于越权的行为不警告也不拦截
查询当前 SELinux 服务状态：getenforce

SELinux 安全策略包括域和安全上下文：
- SELinux 域：对进程资源进行限制（ps -Z 查看）
- SELinux 安全上下文：对系统资源进行限制（ls -Z）
SELinux 安全上下文是由冒号间隔的四个字段组成，用户段:角色段:类型段:?

semanag 用于查询和修改 SELinux 的安全上下文：

|参数 |作用|
|-  |-  |
|-l |查询 |
|-a |增加 |
|-m |修改 |
|-d |删除 |

restorecon  用于恢复 SELinux 文件安全上下文

|-i |忽略不存在的文件|
|-e |排除目录 |
|-R |地柜处理 |
|-v |显示详细的过程  |
|-F |强制恢复 |

##### 9.6、个人用户主页功能

查询所有 SELinux 规则的布尔值，格式为: getsebool -a; getsebool -a | grep home
- 0 或 off 为禁止
- 1 或 on 为允许
将个人用户网站功能策略设置为允许：setsebool -P httpd_enable_homedirs=on

搜索 SELinux 系统中有关 http 服务的端口号：semanage port -l | grep http









