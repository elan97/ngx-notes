
+ [12.1 HTTPS服务](#12.1HTTPS服务)
	+ [12.1.1 对称加密和非对称加密](#12.1.1对称加密和非对称加密)
	+ [12.1.2 HTTPS加密协议原理](#12.1.2HTTPS加密协议原理)
	+ [12.1.3 中间人伪造劫持原理](#12.1.3中间人伪造劫持原理)
	+ [12.1.4 HTTPS防止中间人劫持原理](#12.1.4HTTPS防止中间人劫持原理)
+ [12.2 自签CA证书生成](#12.2自签CA证书生成)
+ [12.3 HTTPS配置](#12.3HTTPS配置)
+ [12.4 苹果要求的HTTPS配置](#12.4苹果要求的HTTPS配置)
+ [12.5HTTPS服务优化](#12.5HTTPS服务优化)



#### 12.1HTTPS服务

> HTTP缺点

1. 传输数据被中间人盗用、信息泄露  
2. 数据内容劫持、篡改

> HTTPS优点

1. 对传输内容进行加密以及身份验证

##### 12.1.1对称加密和非对称加密

* 对称加密
![][对称加密]

* 非对称加密

![][非对称加密]

##### 12.1.2HTTPS加密协议原理

![][HTTPS加密协议]

##### 12.1.3中间人伪造劫持原理

![][中间人伪造劫持]

##### 12.1.4HTTPS防止中间人劫持原理

![][防止中间人劫持原理]

#### 12.2自签CA证书生成

* 使用`openssl`签发.

1. 生成key密钥.
2. 生成证书签名请求文件(csr文件)
3. 生成证书签名文件(CA文件)

```bahs
#生成idea
openssl genrsa -idea -out domcc.key 1024 #按要求输入自定义密码

#生成csr请求文件
openssl req -new -key  domcc.key -out domcc.csr

#生成crt证书文件
openssl x509 -req -days 60 -in domcc.csr -signkey domcc.key -out domcc.crt

```

#### 12.3HTTPS配置

* `ngx_http_ssl_module`官方文档:<http://nginx.org/en/docs/http/ngx_http_ssl_module.html>

```bash
#配置规则
Syntax:	ssl on | off;
Default:	
ssl off;
Context:	http, server

Syntax:	ssl_certificate file;
Default:	—
Context:	http, server

Syntax:	ssl_certificate_key file;
Default:	—
Context:	http, server

#nginx配置
server{
	listen 443;
	server_name 192.168.0.107 locahost;
	keepalive_timeout 70;

	ssl on;
	ssl_certificate /etc/nginx/ssl-key/test_domcc.crt;
	ssl_certificate_key /etc/nginx/ssl-key/test_domcc.key;
	
	location / {
	root /opt/app/code;
	index index.html index.htm;
	}
}

```

* 自签https效果

![][https-test]

#### 12.4苹果要求的HTTPS配置
* 苹果要求的HTTPS配置规则

1. 服务器所有的连接使用TLS1.2以上版本(openssl 1.0.2)
2. HTTPS证书必须使用SHA256以上哈希算法签名
3. HTTPS证书必须使用RSA 2048位或ECC 256位以上公钥算法
4. 使用前向加密技术

```bash
#生成key
openssl req -mew -key domcc.key -out domcc.csr

#生成crt
openssl req -days 60 -x509 -sha256 -nodes -newkey rsa:2048 -keyout test_domcc.key -out apple_test_domcc.crt

```
> nginx配置crt证书重启后浏览器可查看.


#### 12.5HTTPS服务优化

1. 激活keepalive长连接
2. 设置ssl session缓存

```bash
#nginx配置
keepalive_timeout 100;
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```








[对称加密]:./对称加密.png
[非对称加密]:./非对称加密.png
[HTTPS加密协议]:./HTTPS加密协议.png
[中间人伪造劫持]:./中间人伪造劫持.png
[防止中间人劫持原理]:./防止中间人劫持原理.png
[https-test]:./https-test.png


