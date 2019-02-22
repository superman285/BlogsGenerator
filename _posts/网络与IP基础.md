---
title: 网络与IP基础
tags:
  - HTTP
  - 基础
categories:
  - Web
date: 2018-09-02 20:36:59
---





### 网络与IP

------





<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:28px">HTTP协议规定了内容组织形式(请求4部分、响应4部分)</font>

但没规定怎么传(传输方式)



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:28px">TCP协议规定如何传输</font>

传输控制协议 Transmission Control Protocol



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:28px">IP协议规定如何联网</font>

网络协议 Internet Protocol，有ip才能访问网站



常见面试题：

1. **TCP和UDP区别**

TCP可靠、面向链接、相对UDP较慢；

UDP不可靠、不面向连接、相对TCP较快。

1. **TCP的三次握手**

   每次建立连接前，客户端和服务器端之间都要进行三次对话才开始正式传输内容。

   > 客户端：我要连接你了，可以么？
   >
   > 服务端：我准备好了，连接我吧
   >
   > 客户端：那我连接你咯
   >
   > 开始传输内容



网络相关知识：

路由器连上电信服务器，路由器就有了一个外网IP，是在互联网上的地址。

一般外网IP都不是恒定不变的，固定的外网IP需要花钱租用，例如BAT官网的IP。



路由器创建内网，家里连路由的设备使用内网，一般ip格式都是192.168.xxx.xxx



路由器有外网ip(电信给分配的)和内网IP(192.168.1.1即路由配置页地址)

路由-必经之路，联通了内外网，有时也被叫网关。

若路由器到电信连接中断了，那内网设备就无法上外网了。 

路由作用：交通警察，分发内容，转发内容。



内网中的设备想访问外网必须经过路由器中转

外网中的设备可以互相访问，外网中的设备无法访问你的内网设备。



重要IP：

- 本地(当前设备自己):127.0.0.1,每个设备都有这个ip

  hosts文件有一行：127.0.0.1 localhost，即localhost指向127.0.0.1

- 0.0.0.0 不表示任何设备，不同地方含义不同。



### 端口

------



想要访问一个设备(使用tcp或udp协议，http就使用了tcp)，只指定ip不够，还必须指定端口Port。



一个服务器可能提供不止一种服务，例如既提供HTTP服务、又提供FTP服务、还提供SMTP邮件服务，只用一个IP无法告诉服务器想使用哪种服务，所以需要端口。（如果不用端口那就一个个排队，效率极低)



> **重要原则：一个端口对应一个服务。**

http服务最好使用80端口(可以使用别的端口，但不建议违反约定)

https服务最好使用443端口

FTP服务最好使用21端口



- 0~1023端口留给系统使用，拥有管理员权限后才可使用这1024个端口。
- 其他端口可以给普通用户使用。
- 如果一个端口正在提供服务，被占用了，那就不能再使用这个端口，除非停掉正在占用这个端口的服务。



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:28px">使用HTTP协议访问另一个IP时，必须同时提供IP和端口号，缺一不可。==



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:28px">如果不写端口号，浏览器默认帮你加上端口80。</font>

http协议，浏览器默认加上:80

https协议，浏览器默认加上:443



补充：//判断是http或https 或file(看本地时)

> [//协议](https://blog.csdn.net/jimbowong/article/details/54960244)

### 浏览器地址栏输入网址按回车

------

发生了以下事情：



浏览器先帮你补全协议、端口、路径/等

向DNS发起询问，电信给一个真实IP

浏览器向IP发起HTTP请求

百度服务器接受请求，根据路径不同情况返回不同内容(html或css或js)

下载到html内容后 浏览器解析和渲染html内容呈现在你面前





HTTP响应第四部分（消息体）的格式是由什么确定

❌ 由请求的路径的后缀确定的，如 /index.html 对应的响应就一定是 HTML 文件

✅ 由响应第二部分中的 Content-Type 确定的，如 Content-Type: text/html 表示 HTML 文件



保证内容格式需要确保HTTP头的 Content-Type，后缀真的无所谓。



HTTP路径不是文件路径！！



Node.js设置响应各部分内容API:

- 第一部分: response.statusCode = 200
- 第二部分: response.setHeader
- 第四部分(消息体): response.write('Hi')



若启动了http-server但 浏览器无法访问localhost:端口号/

可以检查下hosts文件配置是否有问题