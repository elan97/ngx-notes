
+ [14.1 nginx架构的安全](#14.1nginx架构的安全)
+ [14.2 SQL注入场景](#14.2SQL注入场景)
+ [14.3 Nginx+Lua防火墙](#14.3Nginx+Lua防火墙)




#### 14.1nginx架构的安全

1. 常见的恶意行为

* 爬虫行为和恶意抓取、资源盗用
* 基础防盗链功能-目的不让恶意用户能轻易的爬取网站对外数据  
* secure_link_module-对数据安全性提高加密验证和失效性,适合如核心重要数据.  
* access_module-对后台、部分用户服务的数据提供IP防控

2. 常见的应用层攻击手段

* 后台密码撞库通过猜测密码字典不断对后台系统登录性尝试获取后台登录密码  
* 文件上传漏洞-利用这些可以上传的接口将恶意代码植入到服务器中,再通过url去访问以执行代码

![][upload]  

* SQL注入-利用未过滤/未审核用户输入的攻击方法,让应用运行本不应该运行的SQL代码

3. Nginx防攻击策略
4. 场景: Nginx+LUA的安全waf防火墙

#### 14.2SQL注入场景
![][sql-injection]
![][injection]


#### 14.3Nginx+Lua防火墙
![][lua-waf]

* `ngx_lua_waf`下载地址:<https://github.com/loveshell/ngx_lua_waf>






[upload]:./upload.png
[sql-injection]:./sql-injection.png
[injection]:./injection.png
[lua-waf]:./lua-waf.png
