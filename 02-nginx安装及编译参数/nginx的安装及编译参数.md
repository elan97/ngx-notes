+ [Nginx版本介绍](#Nginx版本介绍)
+ [2.1 Yum安装Nginx](#Nginx版本介绍)
+ [2.2 Nginx的安装目录](#2.2Nginx的安装目录)
	+ [2.2.1 目录介绍](#2.2.1目录介绍)
+ [2.3 编译安装的参数](#2.3编译安装的参数)


### Nginx安装以及编译安装的参数介绍

#### Nginx版本介绍
1. Mainline version  -  开发版
2. Stable version	 -  稳定版本
3. Legacy version 	 -	 历史版本  

Nginx获取地址:<http://nginx.org/en/download.html>

#### 2.1Yum安装Nginx
```bash
#添加Nginx安装源
#源获取:http://nginx.org/en/linux_packages.html#RHEL-CentOS
vim /etc/yum.repos.d/nginx.repo
#添加 nginx 源
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
#----------
#查看源 nginx 版本
yum list|grep nginx
#安装 nginx
yum install nginx
#安装成功后可使用此命令查看 nginx 版本号
nginx -v

```
> Yum安装 nginx 简单快捷,没有源码安装繁琐.

#### 2.2Nginx的安装目录
```bash
#使用这个命令可以列出nginx的安装目录
rpm -ql nginx
```
##### 2.2.1目录介绍
![][logrotate目录图]
![][conf配置目录图]
![][cgi配置目录图]
![][type目录图]
![][守护进程配置目录图]
![][module目录图]
![][命令目录图]
![][cache目录图]
![][log目录图]

#### 2.3编译安装的参数
![][make参数图]
![][temp参数图]
![][user参数图]
![][cflags参数图]
![][附加参数图]












[logrotate目录图]:./logrotate目录.png
[conf配置目录图]:./conf配置目录.png
[cgi配置目录图]:./cgi配置目录.png
[type目录图]:./type目录.png
[守护进程配置目录图]:./守护进程配置目录.png
[module目录图]:./module目录.png
[命令目录图]:./命令目录.png
[cache目录图]:./cache目录.png
[log目录图]:./log目录.png
[make参数图]:./make参数.png
[temp参数图]:./temp参数.png
[user参数图]:./user参数.png
[cflags参数图]:./cflags参数.png
[附加参数图]:./附加参数.png



