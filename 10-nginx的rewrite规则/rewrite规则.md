+ [10.1 rewrite规则](#10.1rewrite规则)
	+ [10.1.1 应用场景](#10.1.1应用场景)
+ [10.2 rewrite规则](#10.2rewrite规则)
+ [10.3 正则表达式](#10.3正则表达式)
+ [10.4 flag标记](#10.4flag标记)
	+ [10.4.1 break和last测试](#10.4.1break和last测试)
	+ [10.4.2 redirect和permanent测试](#10.4.2redirect和permanent测试)
+ [10.5 rewrite场景测试](#10.5rewrite场景测试)
+ [10.6 rewrite规则的优先级](#10.6rewrite规则的优先级)
+ [10.7 优雅的书写rewrite规则](#10.7优雅的书写rewrite规则)


#### 10.1rewrite规则
> 实现url重写以及重定向

##### 10.1.1应用场景

1. URL访问跳转.支持开发设计.页面跳转,兼容性支持.展示效果等!
2. SEO优化.
3. 维护.后台维护,流量转发等
4. 安全.

#### 10.2rewrite规则

* `ngx_http_rewrite_module`官方文档:<http://nginx.org/en/docs/http/ngx_http_rewrite_module.html#rewrite>

```bash
#配置规则
Syntax:	rewrite regex replacement [flag];
Default:	—
Context:	server, location, if

```

#### 10.3正则表达式
> 常用的正则表达式

![][regular-expression]
![][regular-expression2]
![][regular-expression3]
![][regular-expression4]

#### 10.4flag标记
![][flag]

##### 10.4.1break和last测试

```bash
#nginx配置
location ~ ^/break {
	rewrite ^/break /test/ break;
}

location ~ ^/last {
	rewrite ^/last /test/ last;
}

location /test/ {
	default_type application/json;
	return 200 '{"status":"success"}';
}
```
> 测试访问:`http://192.168.0.104/break`返回结果

![][break] 

> 测试访问:`http://192.168.0.104/last`返回结果

![][last]


> 1. 匹配到同样的`location`以后,`last`会重新请求服务端,请求服务端地址变成以`test`结尾的路径地址,即查找匹配并重新跳转到对应的`location`.相当于模拟了另一次请求.  
> 2. `break`只会停留在当级`location`下然后找`/test/`,不会往下再查找对应的`location`的规则或者匹配.所以nginx会返回404.

##### 10.4.2redirect和permanent测试
* redirect测试


```bash
#nginx配置
    location ~ ^/break {
	rewrite ^/break /test/ break;
    }

    location ~ ^/last {
	#rewrite ^/last /test/ last;
	rewrite ^/last /test/ redirect;
    }

    location /test/ {
	default_type application/json;
	return 200 '{"status":"success"}';
    }

```
* 请求头结果

```bash
#请求头
XiaoQiYadeMacBook-Pro:~ xiaoqi$ curl -vL http://192.168.0.104/last
*   Trying 192.168.0.104...
* TCP_NODELAY set
* Connected to 192.168.0.104 (192.168.0.104) port 80 (#0)
> GET /last HTTP/1.1
> Host: 192.168.0.104
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 302 Moved Temporarily
< Server: nginx/1.14.2
< Date: Wed, 27 Mar 2019 06:55:12 GMT
< Content-Type: text/html
< Content-Length: 161
< Location: http://192.168.0.104/test/
< Connection: keep-alive
< 
* Ignoring the response-body
* Connection #0 to host 192.168.0.104 left intact
* Issue another request to this URL: 'http://192.168.0.104/test/'
* Found bundle for host 192.168.0.104: 0x7fb858d086b0 [can pipeline]
* Re-using existing connection! (#0) with host 192.168.0.104
* Connected to 192.168.0.104 (192.168.0.104) port 80 (#0)
> GET /test/ HTTP/1.1
> Host: 192.168.0.104
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< Server: nginx/1.14.2
< Date: Wed, 27 Mar 2019 06:55:12 GMT
< Content-Type: application/json
< Content-Length: 20
< Connection: keep-alive
< 
* Connection #0 to host 192.168.0.104 left intact
{"status":"success"}

```
* permanent测试

```bash
#nginx配置
location ~ ^/i7dom {
	rewrite ^/i7dom http://www.i7dom.cn/ permanent;
}
```
* 请求头信息

```bash
XiaoQiYadeMacBook-Pro:~ xiaoqi$ curl -vL http://192.168.0.104/i7dom
*   Trying 192.168.0.104...
* TCP_NODELAY set
* Connected to 192.168.0.104 (192.168.0.104) port 80 (#0)
> GET /i7dom HTTP/1.1
> Host: 192.168.0.104
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 301 Moved Permanently
< Server: nginx/1.14.2
< Date: Wed, 27 Mar 2019 07:30:42 GMT
< Content-Type: text/html
< Content-Length: 185
< Connection: keep-alive
< Location: http://www.i7dom.cn/
< 
* Ignoring the response-body
* Connection #0 to host 192.168.0.104 left intact
* Issue another request to this URL: 'http://www.i7dom.cn/'
*   Trying 58.222.18.25...
* TCP_NODELAY set
* Connected to www.i7dom.cn (58.222.18.25) port 80 (#1)
> GET / HTTP/1.1
> Host: www.i7dom.cn
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 301 Moved Permanently
< Server: marco/2.9
< Date: Thu, 28 Mar 2019 08:02:23 GMT
< Content-Type: text/html
< Content-Length: 182
< Connection: keep-alive
< Location: https://www.i7dom.cn/
< Via: M.ctn-js-taz-003
< X-Request-Id: cf2168f6f48bc1f6b5750285973e355f
< 
< HTTP/2 200 
< server: marco/2.9
< date: Thu, 28 Mar 2019 08:02:24 GMT
< content-type: text/html; charset=UTF-8
< vary: Accept-Encoding
< x-source: C/200
< x-powered-by: PHP/7.0.28
< x-pingback: https://www.i7dom.cn/action/xmlrpc
< x-request-id: 44442471001bc8a7da2d43045af07675
< via: S.mix-sd-dst1-079, T.79.D, V.mix-sd-dst1-079, T.4.D, M.ctn-js-taz-007
< 

```

> 1. 永久重定向:客户端会永久保存重定向结果,如果重定向服务器关了也会重定向到重定向页面,除非清理浏览器缓存.  
> 2. 临时重定向:会重新向服务器发起请求.


#### 10.5rewrite场景测试

```bash
#nginx配置
location / {
	rewrite ^/static-(\d+)-(\d+)-(\d+)\.html /static/$1/$2/static-$3.html break;
	index index.html;
}

#文件目录
/opt/app/code/static/28/05
```
* 测试结果

![][rewrite-test]

#### 10.6rewrite规则的优先级

> 执行server块的rewrite指令
> 执行location匹配
> 执行选定的location中的rewrite

#### 10.7优雅的书写rewrite规则

![][grace-write]











[regular-expression]:./regular-expression.png
[regular-expression2]:./regular-expression2.png
[regular-expression3]:./regular-expression3.png
[regular-expression4]:./regular-expression4.png
[flag]:./flag.png
[break]:./break.png
[last]:./last.png
[rewrite-test]:./rewrite-test.png
[grace-write]:./grace-write.png
