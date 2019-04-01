<<<<<<< HEAD
测试
=======
<<<<<<< HEAD
测试
=======
### Nginx笔记目录

----------

1. [Nginx 的I/O模式和优势](./01-nginx中间件/中间件架构及nginx简述.md)
	+ [1.1 环境依赖](#1.1环境依赖)
	+ [1.2 Nginx的中间件架构](#1.2Nginx的中间件架构)
	+ [1.3 I/O类型的理解](#1.3I/O类型的理解)
		- [1.3.1 同步和异步](#1.3.1同步和异步)
		- [1.3.2阻塞和非阻塞](#1.3.2阻塞和非阻塞)
	+ [1.4 I/O模型](#1.4I/O模型)
	+ [1.5 Nginx 优势](#1.5Nginx优势)
		- [1.5.1 什么是IO复用](#1.5.1什么是IO复用)
		- [1.5.2 什么是IO多路复用](#1.5.2什么是IO多路复用)
		- [1.5.3 什么是select](#1.5.3什么是select)
		- [1.5.4 轻量级](#1.5.4轻量级)
		- [1.5.5 CPU亲和性](#1.5.5CPU亲和性)
		- [1.5.6 sendfile](#1.5.6sendfile)
2. [Nginx 安装以及编译安装的参数介绍](./02-nginx安装及编译参数/nginx的安装及编译参数.md)
	+ [Nginx版本介绍](#Nginx版本介绍)
	+ [2.1 Yum安装Nginx](#Nginx版本介绍)
	+ [2.2 Nginx的安装目录](#2.2Nginx的安装目录)
		+ [2.2.1 目录介绍](#2.2.1目录介绍)
	+ [2.3 编译安装的参数](#2.3编译安装的参数)
3. [Nginx 的基础配置语法](./03-nginx的基础配置/Nginx的基础配置.md)
	+ [3.1 Nginx默认配置语法](#3.1Nginx默认配置语法)
		+ [3.1.1 全局性和服务级别解释](#3.1.1全局性和服务级别解释)
		+ [3.1.2 event事件模块解释](#3.1.2event事件模块解释)
		+ [3.1.3 server模块的解释](#3.1.3server模块的解释)
	+ [3.2 HTTP请求](#3.2HTTP请求)
	+ [3.3 Nginx日志类型](#3.3Nginx日志类型)
		+ [3.3.1 log_format语法](#3.3.1log_format语法)
	+ [3.4Nginx变量](#3.4Nginx变量)
		+ [3.4.1log_format变量解释](#3.4.1log_format变量解释)
4. [Nginx 常用模块示例](./04-nginx的模块配置/Nginx模块.md)
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
5. [Nginx 静态资源WEB服务应用场景](./05-nginx静态资源配置/静态资源WEB服务场景.md)
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
6. [Nginx 代理服务配置](./06-nginx代理配置/ 的代理配置.md)
	+ [6.1 代理类型](#6.1代理类型)
	+ [6.2 Nginx反向代理配置](#6.2Nginx反向代理配置)
	+ [6.3 Nginx正向代理 ](#6.3Nginx正向代理 )
	+ [6.4 Nginx反向代理进阶](#6.4Nginx反向代理进阶)
		+ [6.4.1 proxy_buffering配置](#6.4.1proxy_buffering配置)
		+ [6.4.2 proxy_redirect配置](#6.4.2proxy_redirect配置)
		+ [6.4.3 proxy_set_header配置](#6.4.3proxy_set_header配置)
		+ [6.4.4 proxy_connect_timeout配置](#6.4.4proxy_connect_timeout配置)
		+ [6.4.5 代理常用配置方法](#6.4.5代理常用配置方法)
7. [Nginx 负载均衡配置](./07-nginx的负载均分配置/负载均衡配置.md)
	+ [7.1 Nginx负载均衡逻辑](#7.1Nginx负载均衡逻辑)
		+ [7.1.1 基于LVS的中间件架构](#7.1.1基于LVS的中间件架构)
		+ [7.1.2 四层负载均衡](#7.1.2四层负载均衡)
		+ [7.1.3 七层负载均衡](#7.1.3七层负载均衡)
	+ [7.2 Nginx负载均衡配置](#7.2Nginx负载均衡配置)
		+ [7.2.1 负载均衡配置](#7.2.1负载均衡配置)
	+ [7.3 Nginx负载均衡-进阶](#7.3Nginx负载均衡-进阶)
		+ [7.3.1 参数应用例子](#7.3.1参数应用例子)
	+ [7.4 Nginx的调度算法](#7.4Nginx的调度算法)
		+ [7.4.1 加权轮训](#7.4.1加权轮训)
		+ [7.4.2 ip-hash分配](#7.4.2ip-hash分配)
		+ [7.4.3 url-hash和hash关键值](#7.4.3url-hash和hash关键值)
8. [Nginx 缓存服务的配置](./08-nginx缓存服务的配置/缓存服务的配置.md)
	+ [8.1 缓存服务器的类型](#8.1缓存服务器的类型)
	+ [8.2 代理缓存的配置](#8.2代理缓存的配置)
	+ [8.3 清理指定缓存](#8.3清理指定缓存)
	+ [8.4 部分页面不缓存](#8.4部分页面不缓存)
	+ [8.5 大文件分片请求](#8.5大文件分片请求)
9. [Nginx 动静分离配置](./09-nginx动静分离配置/动静分离的配置.md)
	+ [9.1 动静分离](#9.1动静分离)
	+ [9.2 动静分离配置](#9.2动静分离配置)
		+ [9.2.1 动态服务配置](#9.2.1动态服务配置)
		+ [9.2.2 静态服务配置](#9.2.2静态服务配置)
10. [Nginx rewrite规则](./10-nginx的rewrite规则/rewrite规则.md)
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
11. [Nginx 高级模块配置](./11-nginx高级模块配置/高级模块配置.md)
	+ [11.1 secure_link模块](#11.1secure_link模块)
		+ [11.1.1 secure_link模块配置](#11.1.1secure_link模块配置)
	+ [11.2 geoip模块](#11.2geoip模块)
		+ [11.2.3 geoip2配置](#11.2.3geoip2配置)
12. [Nginx HTTPS配置](./12-nginx的https配置)
	+ [12.1 HTTPS服务](#12.1HTTPS服务)
		+ [12.1.1 对称加密和非对称加密](#12.1.1对称加密和非对称加密)
		+ [12.1.2 HTTPS加密协议原理](#12.1.2HTTPS加密协议原理)
		+ [12.1.3 中间人伪造劫持原理](#12.1.3中间人伪造劫持原理)
		+ [12.1.4 HTTPS防止中间人劫持原理](#12.1.4HTTPS防止中间人劫持原理)
	+ [12.2 自签CA证书生成](#12.2自签CA证书生成)
	+ [12.3 HTTPS配置](#12.3HTTPS配置)
	+ [12.4 苹果要求的HTTPS配置](#12.4苹果要求的HTTPS配置)
	+ [12.5HTTPS服务优化](#12.5HTTPS服务优化)
13. [Nginx Lua开发](./13-nginx与lua开发/Nginx与Lua开发.md)
	+ [13.1 Lua介绍](#13.1Lua介绍)
		+ [13.1.1 安装lua解释器](#13.1.1安装lua解释器)
	+ [13.2 Lua基础语法](#13.2Lua基础语法)
	+ [13.3 Nginx+Lua环境](#13.3Nginx+Lua环境)
		+ [13.3.1 编译Lua模块过程](#13.3.1编译Lua模块过程)
	+ [13.4 常用的Lua指令](#13.4常用的Lua指令)
	+ [13.5 灰度发布](#13.5灰度发布)
	+ [13.6 lua简单的使用](#13.6lua简单的使用)
14. [Nginx 架构场景](./14-nginx架构场景/nginx架构场景.md)
	+ [14.1 多虚拟主机的优先级](#14.1多虚拟主机的优先级)
	+ [14.2 location匹配优先级](#14.2location匹配优先级)
	+ [14.3 try_files的使用](#14.3try_files的使用)
	+ [14.4 alias和root的区别](#14.4alias和root的区别)
	+ [14.5 获取用户的真是IP](#14.5获取用户的真是IP)
	+ [14.6 nginx常见的错误码](#14.6nginx常见的错误码)
	+ [14.7 nginx性能优化](#14.7nginx性能优化)
		+ [14.7.1 性能优化考虑点](#14.7.1性能优化考虑点)
		+ [14.7.2 系统与nginx性能优化](#14.7.2系统与nginx性能优化)
		+ [14.7.3 CPU亲和](#14.7.3CPU亲和)
15. [Nginx 架构的安全](./15-nginx架构的安全/nginx架构的安全.md)
	+ [14.1 nginx架构的安全](#14.1nginx架构的安全)
	+ [14.2 SQL注入场景](#14.2SQL注入场景)
	+ [14.3 Nginx+Lua防火墙](#14.3Nginx+Lua防火墙)
16. [架构总结](./16-架构总结/架构总结.md)
		
		
		
		

>>>>>>> first
>>>>>>> first