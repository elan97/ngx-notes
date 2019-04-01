+ [3.1 Nginx默认配置语法](#3.1Nginx默认配置语法)
	+ [3.1.1 全局性和服务级别解释](#3.1.1全局性和服务级别解释)
	+ [3.1.2 event事件模块解释](#3.1.2event事件模块解释)
	+ [3.1.3 server模块的解释](#3.1.3server模块的解释)
+ [3.2 HTTP请求](#3.2HTTP请求)
+ [3.3 Nginx日志类型](#3.3Nginx日志类型)
	+ [3.3.1 log_format语法](#3.3.1log_format语法)
+ [3.4Nginx变量](#3.4Nginx变量)
	+ [3.4.1log_format变量解释](#3.4.1log_format变量解释)

### Nginx的基础配置语法
-----



#### 3.1Nginx默认配置语法
![][nginx默认语法]

> `nginx.conf`作为主要的配置文件  
> `include /etc/nginx/conf.d/*.conf`这个包含文件会把`conf.d`目录下以`.conf`结尾的后缀配置文件全部包含到主配置文件中.

##### 3.1.1全局性和服务级别解释
```nginx
user		设置使用用户
worker_processes		进行增大并发连接数的处理,跟CPU核心数保持一致
pid			nginx服务启动时候的pid
```
##### 3.1.2event事件模块解释
```nginx
worker_connections	一个进程允许处理的最大连接数
use		定义使用的内核模型
```
##### 3.1.3server模块的解释
```nginx
listen		监听的端口
server_name		主机名或者是域名
location		控制每一层访问的路径
root	首页的路径
index	首页默认访问那个页面
error_page 500 502 503 504 /50x.html		错误页面 前面的500是'http状态码'
```
#### 3.2HTTP请求
![][http请求]

```bash
curl http://www.baidu.com		返回请求内容

curl -v http://www.baidu.com >/dev/null  重定向空设备可查看隐藏头信息

#request信息
> GET / HTTP/1.1  				请求协议
> User-Agent: curl/7.29.0		请求设备
> Host: www.baidu.com			请求地址
> Accept: */*						请求的类型,这里是任意类型

#response信息
< HTTP/1.1 200 OK		200服务端正常相应
< Accept-Ranges: bytes
< Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
< Connection: Keep-Alive
< Content-Length: 2381
< Content-Type: text/html
< Date: Sun, 24 Mar 2019 04:05:06 GMT
< Etag: "588604cf-94d"
< Last-Modified: Mon, 23 Jan 2017 13:27:43 GMT
< Pragma: no-cache
< Server: bfe/1.0.8.18
< Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/


```
#### 3.3Nginx日志类型
> error.log	记录处理http请求的错误状态以及nginx本身服务的错误状态  
> access_log	每次http请求的访问状态

##### 3.3.1log_format语法
* `log_format`依赖`ngx_http_log_module`

```nginx
#log_format语法
Syntax:	log_format name [escape=default|json|none] string ...;
Default:	
log_format combined "...";
Context:	http

```
* access_log 配置格式

![][log_format_accesslog]

```bash
#access_log输出预览
192.168.0.106 - - [24/Mar/2019:14:54:48 +0800] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.75 Safari/537.36" "-"

```
#### 3.4Nginx变量
![][nginx变量]

```bash
#access.log中输出user-agent值
log_format  main '$http_user_agent'  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';

#日志输出类型预览
curl/7.29.0127.0.0.1 - - [24/Mar/2019:15:08:36 +0800] "GET / HTTP/1.1" 200 612 "-" "curl/7.29.0" "-"

```
> Nginx内置变量:<http://nginx.org/en/docs/http/ngx_http_core_module.html#var_status>

##### 3.4.1log_format变量解释

* log_format main日志变量的解释

```bash
$remote_addr		表示客户端的地址
$remote_user		表示客户端请求nginx认证的用户名
$time_local		表示时间
$request			表示request请求头
$status			表示response的返回状态
$body_bytes_sent	表示返回数据的大小
$http_referer		表示上级页面的地址
$http_user_agent	表示user客户端的信息
$http_x_forwarded_for	表示每一级请求携带的信息
```




[nginx默认语法]:./nginx默认语法.png
[http请求]:./http请求.png
[log_format_accesslog]:./log_format_accesslog.png
[nginx变量]:./nginx变量.png