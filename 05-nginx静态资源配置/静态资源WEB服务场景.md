+ [5.1 静态资源WEB服务](#5.1静态资源WEB服务)
+ [5.2 静态资源的类型](#5.2静态资源的类型)
+ [5.3 静态资源服务场景-CDN](#5.3静态资源服务场景-CDN)
+ [5.4 文件读取配置](#5.4文件读取配置)
	+ [5.4.1 sendfile配置](#5.4.1sendfile配置)
	+ [5.4.2 tcp_nopush配置](#5.4.2tcp_nopush配置)
	+ [5.4.3 tcp_nodelay配置](#5.4.3tcp_nodelay配置)
	+ [5.4.4 gzip压缩](#5.4.4gzip压缩)
	+ [5.4.5 扩展Nginx压缩-预压gzip](#5.4.5扩展Nginx压缩-预压gzip)
	+ [5.4.6 图片无压缩预览效果](#5.4.6图片无压缩预览效果)
	+ [5.4.7 图片压缩后效果预览](#5.4.7图片压缩后效果预览)
	+ [5.4.8 文本无压缩预览](#5.4.8文本无压缩预览)
	+ [5.4.9 文本压缩预览](#5.4.9文本压缩预览)
	+ [5.4.10 预读压缩预览](#5.4.10预读压缩预览)
+ [5.5浏览器缓存](#5.5浏览器缓存)
	+ [5.5.1 校验过期机制](#5.5.1校验过期机制)
	+ [5.2.2.2开启缓存过期验证](#5.2.2.2开启缓存过期验证)
+ [5.6 跨站访问](#5.6跨站访问)
	+ [5.6.1 add_header设置](#5.6.1add_header设置)
+ [5.7 防盗链设置](#5.7防盗链设置)
+ [5.7.1 http_referer设置](#5.7.1http_referer设置)



### 静态资源WEB服务应用场景
-----------

#### 5.1静态资源WEB服务
![][静态资源WEB服务]

#### 5.2静态资源的类型
* 非服务器动态运行生成的文件

![][static-file]

#### 5.3静态资源服务场景-CDN
![][cnd-out]

#### 5.4文件读取配置

* 
##### 5.4.1sendfile配置
* `sendfile`模块官方文档:<http://nginx.org/en/docs/http/ngx_http_core_module.html#sendfile>

```bash
#sendfile配置规则
Syntax:	sendfile on | off;
Default:	
sendfile off;
Context:	http, server, location, if in location

```
* 随着nginx版本的越来越高,nginx支持`--with-file-aio`**异步文件读取**

##### 5.4.2tcp_nopush配置

* `tcp_nopush`模块官方文档:<http://nginx.org/en/docs/http/ngx_http_core_module.html#tcp_nopush>

```bash
#tcp_nopush配置规则
Syntax:	tcp_nopush on | off;
Default:	
tcp_nopush off;
Context:	http, server, location

```

* 作用:`sendfile`开启的情况下,提高网络包的传输效率

##### 5.4.3tcp_nodelay配置
* `tcp_nodelay`模块官方文档:<http://nginx.org/en/docs/http/ngx_http_core_module.html#tcp_nodelay>

```bash
#tcp_nodelay配置规则
Syntax:	tcp_nodelay on | off;
Default:	
tcp_nodelay on;
Context:	http, server, location

```

* 作用:keepalive链接下,提高网络包的传输实时性

##### 5.4.4gzip压缩
* `ngx_http_gzip_module`模块文档地址:<http://nginx.org/en/docs/http/ngx_http_gzip_module.html>

```bash
#gzip配置规则
Syntax:	gzip on | off;
Default:	
gzip off;
Context:	http, server, location, if in location

#gzip压缩等级
Syntax:	gzip_comp_level level;
Default:	
gzip_comp_level 1;
Context:	http, server, location

#http版本
Syntax:	gzip_http_version 1.0 | 1.1;
Default:	
gzip_http_version 1.1;
Context:	http, server, location

```
![][gzip]

* 作用:压缩传输,压缩等级越高.消耗的性能越大!

##### 5.4.5扩展Nginx压缩-预压gzip

* `ngx_http_gzip_static_module`模块文档:<http://nginx.org/en/docs/http/ngx_http_gzip_static_module.html>
* `ngx_http_gunzip_module`模块文档:<http://nginx.org/en/docs/http/ngx_http_gunzip_module.html>


```bash
#gzip_static配置规则
Syntax:	gzip_static on | off | always;
Default:	
gzip_static off;
Context:	http, server, location

```

##### 5.4.6图片无压缩预览效果
```bash
    listen       80;
    server_name  localhost;

    sendfile on;
    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location ~ .*\.(jpg|gif|png)$ {
    #gzip on;
    #gzip_http_version 1.1;
    #gzip_comp_level 2;
    #gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    root /opt/app/code/images;
    }

    location ~ .*\.(txt|xml)$ {
    #gzip on;
    #gzip_http_version 1.1;
    #gzip_comp_level 1;
    #gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/
    # javascript application/x-httpd-php image/jpeg image/gif image/png;
    root /opt/app/code/doc;
    }

    location ~ ^/download {
    #gzip_static on;
    tcp_nopush on;
    root /opt/app/code;
    }

```

* 效果预览


![][nogzip]

##### 5.4.7图片压缩后效果预览
```bash
    listen       80;
    server_name  localhost;

    sendfile on;
    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location ~ .*\.(jpg|gif|png)$ {
    gzip on;
    gzip_http_version 1.1;
    gzip_comp_level 2;
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    root /opt/app/code/images;
    }

    location ~ .*\.(txt|xml)$ {
    #gzip on;
    #gzip_http_version 1.1;
    #gzip_comp_level 1;
    #gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/
    # javascript application/x-httpd-php image/jpeg image/gif image/png;
    root /opt/app/code/doc;
    }

    location ~ ^/download {
    #gzip_static on;
    tcp_nopush on;
    root /opt/app/code;
    }
```
* 压缩对比图

![][img-gzip]

##### 5.4.8文本无压缩预览
```bash
    location ~ .*\.(txt|xml)$ {
    #gzip on;
    #gzip_http_version 1.1;
    #gzip_comp_level 1;
    #gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/
    # javascript application/x-httpd-php image/jpeg image/gif image/png;
    root /opt/app/code/doc;
    }

    location ~ ^/download {
    #gzip_static on;
    tcp_nopush on;
    root /opt/app/code;
    }

#如果出现配置正确访问出现404则文件用户需要修改为nginx
chown nginx [文件名]
```

* 效果预览

![][access-nogzip] 

##### 5.4.9文本压缩预览
```bash
    location ~ .*\.(txt|xml)$ {
    gzip on;
    gzip_http_version 1.1;
    gzip_comp_level 1;
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    root /opt/app/code/doc;
    }
    
```

* 效果预览

![][access-gzip]


##### 5.4.10预读压缩预览
```bash
    location ~ ^/download {
    gzip_static on;
    tcp_nopush on;
    root /opt/app/code;
    }

```

* 效果预览

![][static-gzipa]

> 如果`gzip_static off`的话直接访问`http://192.168.0.105/download/test.img`是404并且不会下载.

#### 5.5浏览器缓存
> HTTP协议定义的缓存机制(如:Expires Cache-control等)

* 浏览器无缓存请求流程图
 
![][browser-no-cache]

* 浏览器有缓存请求流程图

 ![][browser-cache]

##### 5.5.1校验过期机制

![][过期机制]
![][缓存检测流传图]

##### 5.5.2expires配置
* 添加`Cache-Control, Expires`头
* `ngx_http_headers_module`官方文档:<http://nginx.org/en/docs/http/ngx_http_headers_module.html>


```bash
#配置规则
Syntax:	expires [modified] time;
expires epoch | max | off;
Default:	
expires off;
Context:	http, server, location, if in location

#配置方式
location ~ .*\.(html|htm)$ {
	#expires 2m;
	root /opt/app/code;
}
```
###### 5.5.2.1未开启缓存过期

* 第一次请求结果

![][last-request]

* 第二次请求结果

![][second-request]

###### 5.2.2.2开启缓存过期验证
```bash
location ~ .*\.(html|htm)$ {
	expires 2m;
	root /opt/app/code;
}
```
![][cache-request]


#### 5.6跨站访问

* 跨域访问

![][跨域访问]

* 为什么浏览器禁止跨站访问

> 跨站访问不安全,容易出现CSRF攻击!

![][跨站访问-CSRF]

* `ngx_http_headers_module`官方文档:<http://nginx.org/en/docs/http/ngx_http_headers_module.html>

##### 5.6.1add_header设置
```bash
#配置规则
Syntax:	add_header name value [always];
Default:	—
Context:	http, server, location, if in location

#Access-Control-Allow-Origin

#跨站请求代码
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>测试ajax和跨域访问</title>
<script src="http://libs.baidu.com/jquery/2.1.4/jquery.min.js"></script>
</head>
<script type="text/javascript">
$(document).ready(function(){
	$.ajax({
		type: "GET",
		url: "http://192.168.0.105/1.html",
		success: function(data) {
				alert("sucess!!!") ;
		},
		error: function(){ 
			alert("fail!!!,请刷新再试!");
		}
	});
});
</script>
<body>
	<h1>测试跨域访问</h1>
</body>
</html>

#配置方法
location ~ .*\.(html|htm)$ {
	add_header Access-Control-Allow-Origin http://192.168.0.105;
	add_header Access-Control-Allow-Methods GET,POST,PUT,DELETE,OPTIONS;
	root /opt/app/code;
	}
```

* 访问效果

![][CSRF-request-img] 
 
#### 5.7防盗链设置
> 目的:防止资源被盗用.  
> 防盗链设置思路:区别那些请求是非正常用户的请求.

##### 5.7.1http_referer设置
* `ngx_http_referer_module`模块官方文档:<http://nginx.org/en/docs/http/ngx_http_referer_module.html>

```bash
#设置规则
Syntax:	valid_referers none | blocked | server_names | string ...;
Default:	—
Context:	server, location

#配置方法
location ~ .*\.(jpg|gif|png)$ {
	gzip on;
	gzip_http_version 1.1;
	gzip_comp_level 2;
	gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;

	valid_referers none blocked 192.168.0.105;
	if ($invalid_referer) { #如果$invalid_referer非0 则返回403
		return 403;
	}

	root /opt/app/code/images;
}

```

* 测试结果

![][test-request]

> `referer`防盗功能有限,

















[静态资源WEB服务]:./静态资源WEB服务.png
[static-file]:./static-file.png
[cnd-out]:./cnd-out.png
[gzip]:./gzip.png
[nogzip]:./nogzip.png
[img-gzip]:./img-gzip.png
[access-nogzip]:./access-nogzip.png
[access-gzip]:./access-gzip.png
[static-gzipa]:./static-gzipa.png
[browser-no-cache]:./browser-no-cache.png
[browser-cache]:./browser-cache.png
[过期机制]:./过期机制.png
[缓存检测流传图]:./缓存检测流传图.png
[last-request]:./last-request.png
[second-request]:./second-request.png
[cache-request]:./cache-request.png
[跨域访问]:./跨域访问.png
[跨站访问-CSRF]:./跨站访问-CSRF.png
[CSRF-request-img]:./CSRF-request-img.png
[test-request]:./test-request.png