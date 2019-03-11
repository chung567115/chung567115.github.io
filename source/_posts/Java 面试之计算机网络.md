---
title: Java面试之计算机网络
categories: 
  - 笔经面经
tags:
  - Java
  - 校招
date: 2018-07-21 11:12:57
---

> 笔经面经归纳汇总：计算机网络

<!-- more -->

# Java面试之计算机网络
## OSI与TCP/IP各层 
![OSI与TCP/IP结构][7]

## TCP、HTTP  
### TCP三次握手、四次挥手  
#### TCP三次握手（建立连接）
1. 第一次握手：客户端向服务器发送SYN(seq=j)包到服务器；
2. 第二次握手：服务器收到SYN包，向客户端发送ACK包(ack=j+1)和SYN包(seq=k)；
3. 第三次握手：客户端收到SYN＋ACK包，向服务器发送ACK包(ack=k+1)，完成三次握手。 
![三次握手][9]

#### TCP四次挥手（终止连接）
1. 第一次挥手：客户端向服务器发送FIN包(seq=j)，关闭客户到服务器的数据传送；
2. 第二次挥手：服务器收到FIN包，向客户端发送ACK包(ack=j+1)；
3. 第三次挥手：服务器向客户端发送FIN包(seq=k)；
4. 第四次挥手：客户端收到FIN包，向客户端发送ACK包(ack=k+1)，完成四次挥手。
![四次挥手][10]

#### 为什么TCP连接是3次而终止是4次  
&emsp;&emsp;连接时，服务端收到SYN后，把ACK和SYN一起打包回发给客户端，只有3次。   
&emsp;&emsp;终止时，由于TCP连接是全双工的，每个方向都必须单独进行关闭，即FIN和ACK是分开发送的，所以有4次。   
&emsp;&emsp;当一方完成它的数据发送任务后，发送一个FIN来终止连接。但收到一个FIN只表示对方没有数据发送，而己方仍能发送数据。因此必须单独关闭。  

### HTTP相关
#### HTTP的状态码含义  
| 分类 |                          描述                                  |
|:------:|:----------------------------------------------------------:|
| 1xx  | 信息，服务器收到请求，需要请求者继续执行操作   |
| 2xx  | 成功，操作被成功接收并处理                                   |
| 3xx  | 重定向，需要进一步的操作以完成请求                     |
| 4xx  | 请求错误，请求包含语法错误或无法完成请求       |
| 5xx  | 服务器错误，服务器在处理请求时发生错误              |

#### HTTP Request的几种类型  
|    类型     |               描述       | 幂等性  | 安全性 |
|:---------:|:-------------:|:------------:|:------------:|
|POST|向指定资源提交数据进行处理请求（例如提交表单或上传文件） |否 |否 |
| PUT |  向指定资源位置上传其最新内容  | 是 | 否 |
| DELETE  |  请求服务器删除所标识的资源 | 是  | 否 |
|  GET  |  向特定的资源发出请求      | 是 | 是 |
|HEAD|向服务器索要与GET请求相一致的响应，不过响应体不会被返回| 是 | 是 |
|OPTIONS |  返回服务器针对特定资源所支持的HTTP请求方法| 是  | 是 |
|  TRACE   |  回显服务器收到的请求，主要用于测试或诊断  | - | - |
|CONNECT|HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器| - | - |

> 幂等性指该方法多次调用最终资源状态是一样的。
> 安全性指访问资源的时候资源本身不会发生改变。

#### HTTP 1.0、HTTP1.1、HTTP2.0  
- HTTP 1.0中，客户端的每次请求都要建立一次单独的连接，在处理完本次请求后，就自动释放连接。
- HTTP 1.1中，支持**长连接**和**流水线请求**处理，在一个TCP连接上可以传送多个HTTP请求和响应，若干个请求串行化**单线程**处理。
- HTTP2.0中，基于**二进制解析**，可**多路复用**，多个请求可同时在一个连接上**并行处理**。

![HTTP比较][17]

### TCP与HTTP的报文结构  
![tcp报文结构][11]
![http报文结构][12]

## Forward与Redirect  
- 直接转发（Forward）：也称**转发**，浏览器发出请求，服务器把响应内容读取过来，然后再把这些内容发给浏览器。**地址栏显示原来的URL**。
- 间接转发（Redirect）：也称**重定向**，浏览器发出请求，服务器端在响应第一次请求的时候，让浏览器向另一个地址发出请求，然后得到响应。**地址栏显示新的URL**。


## Cookie与Session  
- Cookie：为了辨别用户身份而储存在客户端的数据。服务器在Http响应头中添加Set-Cookie信息，浏览器收到响应后会根据头中的字段保存Cookie，下一次访问时在请求头中附带Cookie内容，供服务器根据Cookie值进行后续处理。
- Session：为了保存用户信息而储存在服务端的数据。当客户端第一次访问时，服务器会计算出一个sessionID，创建一个对应的Session对象并存储；然后将sessionID保存至浏览器Cookie，下一次访问时服务器根据sessionID来匹配Session，Session中可以存一些键值对。

> Cookie最典型的应用是：①判断用户是否登陆过网站，以便下次登录时能够直接登录。②购物车信息处理。


## 访问一个网页全过程  
1. 输入URL；
2. DNS将域名解析成IP地址；
3. 建立TCP连接；
4. OSPF（内部网关协议）决定经过哪些路由器，ARP（地址解析协议）负责求下一个节点地址；
5. 发送HTTP请求；
6. 得到响应，展现HTML界面。

## 使用Socket通信的几个关键函数  
1. 初始化socket：`int socket(int domain, int type, int protocol);`
	- domain ：指定通信协议族。
	- type：指定socket类型。
	- protocol ：协议类型。
	- 返回值：成功返回非负整数。
2. 绑定端口：`int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);`
	- sockfd：socket函数返回的套接字。
	- addr：要绑定的地址。
	- addrlen：地址长度。
	- 返回值：成功返回0，失败返回-1。
3. 监听端口：`int listen(int sockfd, int backlog);`
	- sockfd：socket函数返回的套接字。
	- backlog：已完成三次握手的最大连接个数。
	- 返回值：成功返回0，失败返回-1。
4. 响应连接请求：`int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);`
	- sockfd：服务器套接字。
	- addr：将返回对等方的套接字地址。
	- addrlen：返回对等方的套接字地址长度。
	- 返回值：成功返回非负整数，失败返回-1。
5. 建立连接：`int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);`
	- sockfd：未连接套接字。
	- addr：要连接的套接字地址。
	- addrlen：第二个参数addr长度。
	- 返回值：成功返回0，失败返回-1。

## IP地址分类  

![IP地址分类][8]

## 交换机与路由器区别  
|  比较点  |           交换机        |            路由器             |
|:-------:|:----------------------:|:----------------------------:|
|   作用   |    用于网络内部数据交换   |       用于不同网络间数据传输     |
|  工作层  |        数据链路层        |            网络层             |
|  处理方式 |      硬件处理           |           软件处理            |
|  工作依靠 |      MAC地址           |            IP地址             |



[7]: https://img-blog.csdn.net/20180328110900214?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIxMDIxMDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70

[8]: https://img-blog.csdn.net/20180328112304815?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIxMDIxMDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70

[9]: https://img-blog.csdn.net/20180328121055566?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIxMDIxMDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70

[10]: https://img-blog.csdn.net/20180328121112534?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIxMDIxMDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70

[11]: https://img-blog.csdn.net/20180328123635424?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIxMDIxMDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70

[12]: https://img-blog.csdn.net/20180328123647627?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIxMDIxMDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70

[17]: https://img-blog.csdn.net/20180329132435644?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIxMDIxMDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70
