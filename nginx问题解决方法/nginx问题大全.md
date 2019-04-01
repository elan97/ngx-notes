### nginx问题大全

#### 1.Nginx端口bind失败
> 问题详情:

```bash
#错误日志
2019/03/26 10:55:41 [notice] 10037#10037: signal process started
2019/03/26 10:55:41 [emerg] 7858#7858: bind() to 0.0.0.0:801 failed (13: Permission denied)
```
* 问题分析:nginx默认bind端口策略限制   

> 解决方法:

```bash
#安装semanage
yum install policycoreutils-python
#查看默认策略端口
semanage port -l | grep http_port_t
#结果
[root@localhost nginx]# semanage port -l | grep http_port_t
http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
pegasus_http_port_t            tcp      5988

#添加策略端口
semanage port -a -t http_port_t  -p tcp 8007

#删除策略端口
semanage port -d -t http_port_t  -p tcp 8007

```

> 资料参考:

* StackOverflow:<https://serverfault.com/questions/566317/nginx-no-permission-to-bind-port-8090-but-it-binds-to-80-and-8080>
* linux.die:<https://linux.die.net/man/8/semanage>

#### 2. 关闭防火墙和selinux

```bash
#查看防火墙状态 
firewall-cmd --state
#关闭防火墙
systemctl stop firewalld.service
#禁用防火墙
systemctl disable firewalld.service 

#关闭selinux
vim /etc/sysconfig/selinux
#修改保存
SELINUX=disabled
#查看selinux状态
getenforce 0

```

#### 3.selinux阻止nginx端口

> 问题分析:负载均衡的端口被selinux阻止链接nginx进程

```bash
#nginx错误日志
2019/03/27 00:59:12 [crit] 4359#4359: *18472 connect() to 192.168.0.105:8007 failed (13: Permission denied) while connecting to upstream, client: 192.168.0.106, server: localhost, request: "GET / HTTP/1.1", upstream: "http://192.168.0.105:8007/", host: "192.168.0.104"
2019/03/27 00:59:13 [crit] 4359#4359: *18472 connect() to 192.168.0.105:8007 failed (13: Permission denied) while connecting to upstream, client: 192.168.0.106, server: localhost, request: "GET /favicon.ico HTTP/1.1", upstream: "http://192.168.0.105:8007/favicon.ico", host: "192.168.0.104", referrer: "http://192.168.0.104/"

#SElinux错误日志
type=AVC msg=audit(1553620397.652:590): avc:  denied  { name_connect } for  pid=4359 comm="nginx" dest=8007 scontext=system_u:system_r:httpd_t:s0 tcontext=system_u:object_r:unreserved_port_t:s0 tclass=tcp_socket

```
> 解决方法

```bash
#安装audit2allow
yum install policycoreutils-python

#查看SElinux日志
cat /var/log/audit/audit.log | grep nginx | grep denied

#执行以下命令
setsebool httpd_can_network_connect on
setsebool httpd_can_network_connect on -P

cat /var/log/audit/audit.log | grep nginx | grep denied | audit2allow -M mynginx
semodule -i mynginx.pp


```
