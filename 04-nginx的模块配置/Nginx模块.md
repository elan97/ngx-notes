+ [4.1 Nginx监控模块](#4.1Nginx监控模块)
+ [4.2 Nginx主页随机模块](#4.2Nginx主页随机模块)
+ [4.3 内容替换模块](#4.3内容替换模块)
+ [4.4 请求限制与连接限制模块](#4.4请求限制与连接限制模块)
+ [4.5 访问控制模块](#4.5访问控制模块)
	+ [4.5.1 access_module配置](#4.5.1access_module配置)
	+ [4.5.2 access_module的局限性](#4.5.2access_module的局限性)
		+ [4.5.2.1 x-forwarded-for](#4.5.2.1x-forwarded-for)
		+ [4.5.2.2 解决access模块的局限性](#4.5.2.2解决access模块的局限性)
	+ [4.5.3 auth_basic_module模块](#4.5.3auth_basic_module模块)
		+ [4.5.3.1 auth_basic模块的局限性](#4.5.3.1auth_basic模块的局限性)
		+ [4.5.3.2 auth_basic局限性解决方案](#4.5.3.2auth_basic局限性解决方案)




### Nginx模块
1. Nginx官方模块
2. 第三方模块

#### 4.1Nginx监控模块
* `ngx_http_stub_status_module`文档地址:<http://nginx.org/en/docs/http/ngx_http_stub_status_module.html>

```bash
#配置规则
Syntax:	stub_status;
Default:	—
Context:	server, location

#配置方法
location /mystatus {
    stub_status;
}

#重载配置文件
nginx -s reload -c /etc/nginx/nginx.conf

```
>* 重载配置后访问:<http://192.168.0.105/mystatus>显示状态信息
>![][mystatus]

#### 4.2Nginx主页随机模块
* `ngx_http_random_index_module`文档地址:<http://nginx.org/en/docs/http/ngx_http_random_index_module.html>

```bash
#配置规则
Syntax:	random_index on | off;
Default:	
random_index off;
Context:	location

#配置方法
location / {
	root   /opt/app/code;
	random_index on;
	#index  index.html index.htm;
}

```
>* 重载配置后访问:<http://192.168.0.105>每次刷新后返回一个不同的页面
>![][random]

#### 4.3内容替换模块
* `ngx_http_sub_module`文档地址:<http://nginx.org/en/docs/http/ngx_http_sub_module.html>

```bash
#配置规则1
Syntax:	sub_filter string replacement;
Default:	—
Context:	http, server, location

#配置规则2
Syntax:	sub_filter_last_modified on | off;
Default:	
sub_filter_last_modified off;
Context:	http, server, location
This directive appeared in version 1.5.1.

#配置规则3
Syntax:	sub_filter_once on | off;
Default:	
sub_filter_once on;
Context:	http, server, location

#配置方法
location / {
	root   /usr/share/nginx/html;
	index  index.html index.htm;
	sub_filter '<a>i7dom</a>' '<a>I7DOM</a>';
	sub_filter_once off;
}
```
>* 重载配置后访问:<http://192.168.0.105>后小写`i7dom`替换成大写`I7DOM`
>![][sub]

#### 4.4请求限制与连接限制模块
* `ngx_http_limit_conn_module`链接频率限制模块文档地址:<http://nginx.org/en/docs/http/ngx_http_limit_conn_module.html>
* `ngx_http_limit_req_module`请求频率限制模块文档地址:<http://nginx.org/en/docs/http/ngx_http_limit_req_module.html>

* HTTP协议链接与请求
![][http协议]
![][http协议版本]

```bash
#limit_conn配置规则
Syntax:	limit_conn zone number;
Default:	—
Context:	stream, server

Syntax:	limit_conn_zone key zone=name:size;
Default:	—
Context:	stream

#limit_req配置规则
Syntax:	limit_req_zone key zone=name:size rate=rate [sync];
Default:	—
Context:	http

Syntax:	limit_req zone=name [burst=number] [nodelay | delay=number];
Default:	—
Context:	http, server, location

#http里配置以下内容
limit_conn_zone $binary_remote_addr zone=conn_zone:1m;
limit_req_zone $binary_remote_addr zone=req_zone:1m rate=10r/s;

#location里配置以下内容
limit_req zone=req_zone burst=10 nodelay;  请求限制
limit_conn conn_zone 1;	 	#访问限制

```
>* 重载配置后访问:<http://192.168.0.105>ab压测
>![][ab]

#### 4.5访问控制模块
* `ngx_http_access_module`链接频率限制模块文档地址:<http://nginx.org/en/docs/http/ngx_http_access_module.html>

##### 4.5.1access_module配置
```bash
#access模块配置规则   允许那些访问
Syntax:	allow address | CIDR | unix: | all;
Default:	—
Context:	http, server, location, limit_except
						不允许那些访问
Syntax:	deny address | CIDR | unix: | all;
Default:	—
Context:	http, server, location, limit_except

#access模块的配置			可以配置IP段
location ~ ^/admin.html {
	root   /opt/app/code;
	deny  192.168.0.106;	#禁止这个IP访问
	allow all;		#允许所有IP访问
	index  index.html index.htm;
}

location ~ ^/admin.html {
	root   /opt/app/code;
	allow  192.168.0.106;	#允许这个IP访问
	deny all;		#禁止所有IP访问
	index  index.html index.htm;
}

```
>* 重载配置后访问:<http://192.168.0.105>返回的是403

* 允许所有IP访问,禁止单个

>![][access]

* 禁止所有IP访问,允许单个

>![][allow]

##### 4.5.2access_module的局限性
![][access_module局限性]

###### 4.5.2.1x-forwarded-for
![][x-forwarded-for]

```bash
#x-forwarded-for的格式
http_x_forwarded_for = Client IP,Proxy(1) IP,Proxy(2) IP,...

```
###### 4.5.2.2解决access模块的局限性

![][access+]


##### 4.5.3auth_basic_module模块
* `ngx_http_auth_basic_module`请求频率限制模块文档地址:<http://nginx.org/en/docs/http/ngx_http_auth_basic_module.html>

```bash
#配置规则
Syntax:	auth_basic string | off;
Default:	
auth_basic off;
Context:	http, server, location, limit_except

Syntax:	auth_basic_user_file file;
Default:	—
Context:	http, server, location, limit_except

#生成密码文件
htpasswd -c ./auth_mod [用户名]  #回车输入两次密码

#uth_basic模块配置
location ~ ^/admin.html {
	root   /opt/app/code;
	auth_basic "is need pwd!";
	auth_basic_user_file /etc/nginx/auth_conf;
	ndex  index.html index.htm;
}

```
>* 重载配置后访问:<http://192.168.0.105>
>![][auth_basic]

###### 4.5.3.1auth_basic模块的局限性
1. 用户信息依赖文件方式
2. 操作管理机械,效率低下

###### 4.5.3.2auth_basic局限性解决方案
1. Nginx结合Lua实现高效验证
2. Nginx和Ldap打通,利用nginx-auth-ldap模块





[mystatus]:./mystatus.png
[random]:./random.png
[sub]:./sub.png
[http协议]:./http协议.png
[http协议版本]:./http协议版本.png
[ab]:./ab.png
[access]:./access.png
[allow]:./allow.png
[access_module局限性]:./access_module局限性.png
[x-forwarded-for]:./x-forwarded-for.png
[access+]:./access+.png
[auth_basic]:./auth_basic.png