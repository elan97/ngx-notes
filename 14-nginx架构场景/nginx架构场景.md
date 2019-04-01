+ [14.1 多虚拟主机的优先级](#14.1多虚拟主机的优先级)
+ [14.2 location匹配优先级](#)14.2location匹配优先级
+ [14.3 try_files的使用](#14.3try_files的使用)
+ [14.4 alias和root的区别](#14.4alias和root的区别)
+ [14.5 获取用户的真是IP](#14.5获取用户的真是IP)
+ [14.6 nginx常见的错误码](#14.6nginx常见的错误码)
+ [14.7 nginx性能优化](#14.7nginx性能优化)
	+ [14.7.1 性能优化考虑点](#14.7.1性能优化考虑点)
	+ [14.7.2 系统与nginx性能优化](#14.7.2系统与nginx性能优化)
	+ [14.7.3 CPU亲和](#14.7.3CPU亲和)




### nginx架构场景
1. 相同server_name多个虚拟主机优先级访问  
2. location匹配优先级 
3. try_ files使用
4. Nginx的alias和root区别
5. 用什么方法传递用户的真实IP

#### 14.1多虚拟主机的优先级

```bash
#虚拟主机1
server{
	listen 80;
	server_name testserver1  www.xxx1.com;
	location .. {
		...
	}
}

#虚拟主机2
server{
	listen 80;
	server_name testserver2 www.xxx1.com;
	location ... {
		...
	}
}

```
> * 优先级是以nginx读取第一个虚拟主机配置优先.

#### 14.2location匹配优先级

1. `=` 表示精确匹配。只有请求的url路径与后面的字符串完全相等时，才会命中。
2. `~ `表示该规则是使用正则定义的，区分大小写。
3. `~* `表示该规则是使用正则定义的，不区分大小写。
4. `^~` 表示如果该符号后面的字符是最佳匹配，采用该规则，不再进行后续的查找

```bash
#nginx的location规则配置
location = /code1/ {
	rewrite ^(.*)$ /code1/index.html break;
}

location ~ /code.* {
	rewrite ^(.*)$ /code3/index.html break;
}

location ^~ /code {
	rewrite ^(.*)$ /code2/index.html break;
}

```
> 访问`xxx/code1`时是精确匹配,不会再继续往下匹配.所以访问的是code1的内容.  
> 注释精确匹配后再访问`xxx/code`是正则匹配优先级更高的.所以访问的是code2的内容.  
> 如果注释`code1`和`code2`的匹配模式.才能访问`code3`的内容.  

* 图解location匹配优先级

![][location匹配模式]

#### 14.3try_files的使用
```bash
#按顺序的检查文件是否存在
location / {
	try_files $uri $uri/ /index.php;
}
```
> 其作用是按顺序检查文件是否存在，返回第一个找到的文件或文件夹(结尾加斜线表示为文件夹)，如果所有的文件或文件夹都找不到，会进行一个内部重定向到最后一个参数。  

* 测试结果

```bash
#server1的nginx配置
location / {
	root /opt/app/code/cache/;
	try_files $uri @proxy_pas;
}

location @proxy_pas {
	proxy_pass http://192.168.0.107;
}

#server2的nginx配置
location / {
	root   /opt/app/code1;
	index  index.html index.htm;
}

```
* 测试server1文件存在

![][try_file-server01] 

* 测试server1文件不存在

![][try_file-server02]

> 如果`server`文件存在则,在`server1`服务器发送.否则代理到server2服务器后返回到用户.



* 需要注意的是，只有最后一个参数可以引起一个内部重定向，之前的参数只设置内部URI的指向。最后一个参数是回退URI且必须存在，否则会出现内部500错误.


#### 14.4alias和root的区别
![][root-alias]

#### 14.5获取用户的真是IP
![][Get-ip] 

#### 14.6nginx常见的错误码

* Nginx:413 Request Entity Too Large

> 用户上传文件限制 client_max_body_size

* 502 bad gateway

> 后端服务无响应

* 504 Gateway Time-out

> 后端服务执行超时


#### 14.7nginx性能优化

1. 性能优化考虑点
2. 压测工具 ab
3. 系统与nginx性能优化

##### 14.7.1性能优化考虑点

1. 当前系统结构瓶颈:观察指标 压力测试
2. 了解业务模式: 接口业务类型 系统层次化结构
3. 性能与安全

##### 14.7.1ab接口压测工具

```bash
#安装
yum install httpd-tools
```
* 使用方法

```bash
ab -n 2000 -c 20 http://127.0.0.1
-n 总的请求数
-c 并发数
-k 是否开启长链接
```

##### 14.7.2系统与nginx性能优化

* 文件句柄:lunux\unix一切皆文件,文件句柄就是一个索引
* 系统设置方式:系统全局性修改,用农户局部性修改,进程局部性修改

```bash
#linux修改文件句柄
vim /etc/security/limits.conf 
#设置文件句柄
root soft nofile 65535  #用户 正常设置是1w
root hard nofile 65535
*    soft nofile 65535  #全局
*    hard nofile 65535

#nginx进程设置
worker_rlimit_nofile 65535;
```

##### 14.7.3CPU亲和

* CPU亲和:把进程通常不会在处理器之间频繁迁移进程迁移的频率小,减少性能损耗.

```bash
#查看CPU
cat /proc/cpuinfo #查看CPU信息
cat /proc/cpuinfo|grep "physicl id"|sort|uniq|wc -l

#设置CPU亲和
worker_processes    4;
worker_cpu_affinity 0001 0010 0100 1000;

#查看nginx使用的cpu是那个
ps -eo pid,args,psr | grep [n]ginx

```












[location匹配模式]:./location匹配模式.png
[try_file-server01]:./try_file-server01.png
[try_file-server02]:./try_file-server02.png
[root-alias]:./root-alias.png
[Get-ip]:./Get-ip.png



