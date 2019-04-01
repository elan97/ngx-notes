 
+ [1.1 环境依赖](#1.1环境依赖)
+ [1.2 Nginx的中间件架构](#1.2Nginx的中间件架构)
+ [1.3 I/O类型的理解](#1.3I/O类型的理解)
	+ [1.3.1 同步和异步](#1.3.1同步和异步)
	+ [1.3.2阻塞和非阻塞](#1.3.2阻塞和非阻塞)
+ [1.4 I/O模型](#1.4I/O模型)
+ [1.5 Nginx 优势](#1.5Nginx优势)
	+ [1.5.1 什么是IO复用](#1.5.1什么是IO复用)
	+ [1.5.2 什么是IO多路复用](#1.5.2什么是IO多路复用)
	+ [1.5.3 什么是select](#1.5.3什么是select)
	+ [1.5.4 轻量级](#1.5.4轻量级)
	+ [1.5.5 CPU亲和性](#1.5.5CPU亲和性)
	+ [1.5.6 sendfile](#1.5.6sendfile)

	
### Nginx的I/O模式和优势

--------------------------------------

#### 1.1环境依赖
```bash
#环境Cent OS 7.2
#编译 Nginx 所需的依赖
yum -y install gcc gcc-c++ autoconf pcre pcre-devel make automake zlib zlib-devel openssl openssl-devel
#linux 常用的工具
yum -y install wget vim git httpd-tools
#初始化目录
cd /opt/ && mkdir app backup download logs work
```
#### 1.2Nginx的中间件架构
![][中间件架构图]  

* Nginx（发音同engine x）是一个异步框架的`Web`服务器,也可以用作`反向代理`,`负载平衡器`和`请求分发`.

#### 1.3I/O类型的理解
##### 1.3.1同步和异步
* 关注的是`消息通知`机制  

>同步:调用发出之后`不会立即返回`,但一旦返回,则返回最终结果.  
>异步:调用发出之后,**被调用方`立即返回消息`,但返回的并非最终结果**。被调用者通过状态,通知机制等来通知调用者,或通过回调函数来处理结果.

##### 1.3.2阻塞和非阻塞
* 关注的是`调用者等待被调用者返回调用结果时的状态.`

> 阻塞:`调用结果返回之前,调用者会被挂起`,调用者等待被调用者返回调用结果时的状态.    
> 费阻塞:调用者在结果返回之前,`不会被挂起`.  

---
#### 1.4I/O模型
```C
blocking IO:阻塞式IO
nonblocking IO:非阻塞IO
multiplexing:多路复用IO
signal driven IO:事件驱动式IO
asynchronous IO:异步IO
```
>真正执行IO过程的阶段是内核内存数据拷贝到进程内存中



#### 1.5Nginx优势

>**IO多路复用epoll**  

---
##### 1.5.1什么是IO复用  
 ![][IO复用]

---
##### 1.5.2什么是IO多路复用 
![][IO多路复用图]
> 多个描述符的I/O操作都能在`一个线程内并发交替地顺序完成`,这就叫做I/O多路复用,这里的`复用`是指复用同一个线程.(就像多个学生同时完成作业，同时共享一个老师，学生做完作业举手请求老师。)

--

> **IO多路复用的实现方式 select poll epoll**

##### 1.5.3什么是select
![][select模型图]

>select:`线性遍历`,`效率低`,监视文件描述符的数量存在`最大限制为1024`.  
>epoll:每当`FD`就绪,采用系统的回调函数之间将`FD`放入,效率更高,最大连接无限制.  

* Nginx 采用的是 epoll 模型.

---
##### 1.5.4轻量级
1. 功能模块少.
2. 代码模块化. 

##### 1.5.5CPU亲和性
* 为什么需要CPU亲和  

![][CPU亲和性图]
>是一种把CPU核心和`Nginx`工作进程绑定方式,吧每一个`worker`进程固定在一个CPU上执行,减少切换`CPU`的`cache miss`,获得更好的性能.

---
##### 1.5.6sendfile
* linux 2.2内核前使用的 sendfile 模式

![][sendfile2.2-图]

* linux 2.2内核后使用的 sendfile 模式

![][sendfile2.2+图]

>不通过`User space`,减少静态文件用户空间切换,直接通过内核零拷贝.








[中间件架构图]:../01-nginx中间件/中间件架构.png
[IO复用]:../01-nginx中间件/IO复用.png
[IO多路复用图]:../01-nginx中间件/IO多路复用.jpg
[select模型图]:../01-nginx中间件/seletc模型.png
[CPU亲和性图]:../01-nginx中间件/CPU亲和性.png
[sendfile2.2-图]:../01-nginx中间件/2.2-sendfile.png
[sendfile2.2+图]:../01-nginx中间件/2.2+sendfile.png