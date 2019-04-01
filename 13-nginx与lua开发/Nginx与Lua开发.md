+ [13.1 Lua介绍](#13.1Lua介绍)
	+ [13.1.1 安装lua解释器](#13.1.1安装lua解释器)
+ [13.2 Lua基础语法](#13.2Lua基础语法)
+ [13.3 Nginx+Lua环境](#13.3Nginx+Lua环境)
	+ [13.3.1 编译Lua模块过程](#13.3.1编译Lua模块过程)
+ [13.4 常用的Lua指令](#13.4常用的Lua指令)
+ [13.5 灰度发布](#13.5灰度发布)
+ [13.6 lua简单的使用](#13.6lua简单的使用)





### Nginx与Lua开发


#### 13.1Lua介绍

1. 是一个简洁、轻量、可扩展的脚本语言

* nginx+lua的优势

> 充分的结合Nginx的并发处理epoll优势和Lua的轻量
实现简单的功能切高并发的场景。

##### 13.1.1安装lua解释器
```bash
#CentOS安装
yum install lua

```

#### 13.2Lua基础语法
```lua
#lua的注释
-- 行注释

--[[
块注释
--]]

#lua的变量
a='alo\n123"'
a="alo\n123\""
a='\97lo\10\04923"'
a=[[alo
123"]]
#布尔类型只有nil和false 数字0 空字符串(' \0' )都是true
#lua中的变量如果没有特殊说明.全是全局变量.

#while循环语句
sum = 0
num = 1
while num <= 100 do
	sum = sum + num
	num = num + 1
end
print("sum = ",sum)
#lua没有++ 或是+=这样的操作

#for循环语句
sum =0
for 1=1,100 do
	sum = sum + 1
end

#if-else判断语句
if age ==40 and sex =="Male" then
	print("大于40男人")
elseif age > 60 and sex ~= "Female" then
	print("非女人而且大于60")
else
	local age = io.read()
	print("You age is"..age)
end
# '~='是不等于
# 字符串的拼接操作符 ".."
# io库的分别从stdin和stdout读写的read和write函数

```

#### 13.3Nginx+Lua环境

1. LuaJIT
2. ngx_devel_kit和lua-nginx=module
3. 重新编译Nginx

* http://www.imooc.com/article/19597

##### 13.3.1编译Lua模块过程

> 编译LuaJIT

```bash
#安装编译依赖
yum -y install gcc gcc-c++ autoconf automake
yum -y install zlib zlib-devel openssl openssl-devel pcre-devel

#下载LuaJIT2.0.2
wget http://luajit.org/download/LuaJIT-2.0.2.tar.gz

#解压编译
tar -zxvf LuaJIT-2.0.2.tar.gz 
cd LuaJIT-2.0.2
install PREFIX=/etc/LuaJIT

#添加环境变量
export LUAJIT_LIB=/etc/LuaJIT/lib
export LUAJIT_INC=/etc/LuaJIT/include/luajit-2.0
```

> 编译ngx_devel_kit和lua-nginx-module

* `ua-nginx-module`下载地址:<https://github.com/openresty/lua-nginx-module>
* `ngx_devel_kit`下载地址:<https://github.com/simplresty/ngx_devel_kit>

```bash
#下载模块
wget https://github.com/simpl/ngx_devel_kit/archive/v0.3.0.tar.gz
wget https://github.com/openresty/lua-nginx-module/archive/v0.10.9rc7.tar.gz

#下载nginx
wget http://nginx.org/download/nginx-1.14.2.tar.gz

#添加用户组
groupadd nginx
useradd -g nginx nginx

#编译
cd nginx-1.14.2


./configure \
--prefix=/etc/nginx \
--sbin-path=/usr/sbin/nginx \
--modules-path=/usr/lib64/nginx/modules \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--pid-path=/var/run/nginx.pid \
--lock-path=/var/run/nginx.lock \
--http-client-body-temp-path=/var/cache/nginx/client_temp \
--http-proxy-temp-path=/var/cache/nginx/proxy_temp \
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
--http-scgi-temp-path=/var/cache/nginx/scgi_temp \
--user=nginx \
--group=nginx \
--with-compat \
--with-file-aio \
--with-threads \
--with-http_addition_module \
--with-http_auth_request_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_random_index_module \
--with-http_realip_module \
--with-http_secure_link_module \
--with-http_slice_module \
--with-http_ssl_module \
--with-http_stub_status_module \
--with-http_sub_module \
--with-http_v2_module \
--with-mail \
--with-mail_ssl_module \
--with-stream \
--with-stream_realip_module \
--with-stream_ssl_module \
--with-stream_ssl_preread_module \
--add-module=/root/ngx_devel_kit \
--add-module=/root/lua-nginx-module \

make -j 4 && make install

echo "/etc/LuaJIT/lib" >> /etc/ld.so.conf
ldconfig

```

#### 13.4常用的Lua指令

* Nginx的可插拔模块化加载执行,共11个处理阶段

![][常用lua指令]

* Nignx Lua API

![][nginxLuaAPI]

#### 13.5灰度发布

>按照一定的关系区别,分部分的代码进行上线,使代码的发布能平滑过渡上线.

1. 用户的信息cookie等信息区別
2. 根据用戸的ip地址

![][灰度发布拓扑图]

#### 13.6lua简单的使用

* `nginx lua API`文档:<https://github.com/openresty/lua-nginx-module>


```bash
server {

    listen 80;
    server_name localhost;


    location /ip {
        default_type 'text/plain';
        content_by_lua '
          myip = ngx.req.get_headers()["X-Real-IP"]
          if myip == nil then
              myip = ngx.req.get_headers()["x_forwarded_for"]
          end
          if myip == nil then
              myip = ngx.var.remote_addr
          end
          ngx.say("IP:",myip)
        ';
    }

    location /hello {
        default_type 'text/plain';
        content_by_lua 'ngx.say("hello world")';
    }

}
```
* 测试效果

```bash
[root@localhost nginx]# curl  http://192.168.0.103/ip
IP:192.168.0.107

[root@localhost nginx]# curl  http://192.168.0.103/hello
hello world

```








[常用lua指令]:./常用lua指令.png
[nginxLuaAPI]:./nginxLuaAPI.png
[灰度发布拓扑图]:./灰度发布拓扑图.png