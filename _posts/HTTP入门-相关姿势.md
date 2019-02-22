---
title: HTTP入门&相关姿势
tags:
  - HTTP
  - 基础
categories:
  - Web
date: 2018-09-22 20:34:16
---



### HTTP入门

------

1.www万维网来历

2.HTTP+HTML+URL

3.curl命令

4.请求与响应



==**搞清楚5个概念**==

- 缓存
- 请求
- 响应
- Cookie
- Session



www来历

李爵士发明了第一个网页、第一个浏览器、第一个服务器。



1950年~1960年已经有了互联网雏形，但1990年之前没有网页。

当时人们对互联网的使用集中在email，

1965年email被发明，成为互联网杀手级应用。

1971年@符号被发明

人们用FTP下载文件附件

1980~1990年间人们迫切需要更好的上网方式，很多方案被提出，例如HTTP和Gopher，后来HTTP因为其易用性被提出。

当时的邮件内容都是普通文本或类markdown形式的文本，人们需要一种超文本用作页面跳转，也就是现在说的<a>标签，当时超文本方案有很多，HTML只是其中之一。



这种背景下1989~1992i案件，李爵士发明了world wide web(WWW)，主要包含三个概念：

- URL，统一资源定位符、俗称网址
- HTTP，两个电脑间传输内容的协议
- HTML，超文本，主要用作页面跳转

URL作用是让你访问一个页面，HTTP作用是让你能下载这个页面，HTML作用是让你能看懂这个页面。

三者相加是一个简单而完美的系统

世界上第一个网页 info.cern.ch



URI：统一资源标识符

URL：统一资源定位符    唯一确定的位置

URN：统一资源名称	唯一确定的



最具体的URL会包含以下内容：

- 协议
- 域名
- 端口号(不写的话用默认端口)
- 路径
- 查询参数
- 锚点

<span style="color:darkred;font-weight:700" >至少也要包含协议-域名两部分，例如http://www.baidu.com 才能算得上URL，才能正常定位</span>

(不带协议版本号)

类似

https://localhost:8080/?wd=scss#2



URL的范围更大，既有URI的标识作用，还有地址定位作用，所以URL可以是URI，但是URI却不是URL。

URN和URL 都可以是URI



本地host文件：

127.0.0.1	www.163.com

修改域名指向127.0.0.1 ip地址



> URL是一种具体的URI，它不仅唯一标识资源，而且还提供了定位该资源的信息。URI是一种语义上的抽象概念，可以是绝对的，也可以是相对的，而URL则必须提供足够的信息来定位，所以，是绝对的。



举个栗子🌰

经验式理解：<span style="color:red;">htt-p://localhost:8080</span><span style="color:green">/myweb/hello.html</span>

以上web地址，红色字体部分+绿色字体部分=URL；绿色字体部分=URI（相对的路径)。这是大部分程序员对两者理解的一个典型代表。



www.baidu.com

.com 是一级域名

baidu 是二级域名

www 是三级域名

锚点，跳转到对应id设置处，#5即跳转到id为5的元素那儿



打开html有文件协议和http协议，

从IDE用浏览器打开，http://localhost: , http协议

直接把html文件拖入浏览器打开， file:///C:/Users/SuperX/ , 文件协议

file://  `/c`代表下 最顶层目录下的C盘，



补充：//协议

> [//协议](https://blog.csdn.net/jimbowong/article/details/54960244)





------





#### DNS

------

Domain Name System	域名系统

- ==输入域名==
- ==输出IP==





#### Server + Client + HTTP

------

server服务器，指服务器中运行的代码

client客户端，指用户的电脑中运行的浏览器

服务器和客户端之间互相访问，HTTP协议



开了很多端口，每个端口对应一些服务或功能

1080端口对应代理



输入地址到浏览网页经过的全过程：

首先输入网址www.baidu.com，然后问电信DNSwww.baidu.com对应的IP是多少啊，电信回复一个IP x.x.x.x，然后我们就往这个IP x.x.x.x==发送请求==；服务器server收到请求后==开始响应==给我们返回一个网页，我们的client浏览器就下载这个网页，然后通过一定的规则渲染这个html网页 并展示给用户。

- 浏览器负责发起请求

- 服务器在80端口接收请求

- 服务器负责返回内容(响应)

- 浏览器负责下载响应内容

- ==HTTP的作用就是指导浏览器和服务器如何沟通== HTTP就像调解师

  比如服务器不在线、未开机时 响应502，网址错误或不存在时可能响应404



发起请求

curl -s -v -- "https://www.baidu.com"	默认为get请求方式

curl -X POST -s -v -H "superman285" -- "https://www.baidu.com"

“>”代表请求

"<"代表响应



GET请求

POST请求

POST带数据的请求



💡 找到网页中的图片资源并为我所用:

   Network选项卡，Img标签，选中某个Name点击，可看到Preview预览，选中想要的，双击Name新窗口打开，然后图片另存为即可。



### HTTP相关姿势

---

==**搞清楚5个概念**==

- 缓存
- 请求
- 响应
- Cookie
- Session



==HTTP 的作用就是指导浏览器和服务器如何进行沟通。==



> **curl命令**

curl命令是一个利用URL规则在命令行工作的文件传输工具。

支持文件的上传和下载。

🔔 `curl是发起一个请求`



| 常用命令     | 功能                                                 |
| ------------ | ---------------------------------------------------- |
| -X/--request | 指定请求方式，GET/POST/PUT/PATCH/DELETE/HEAD/OPTIONS |
| -H/--header  | 自定义头信息传递给服务器                             |
| -d/--data    | HTTP POST方式传送数据                                |
| -v/--verbose | 详细输出，包含请求和响应的头部                       |
| -s/--silent  | 静默模式，不显示错误和进度                           |
| -O/          | 从远程下载文件到当前目录，保留原文件名               |



命令使用举:chestnut:



- curl -X POST https://www.baidu.com 

  不带-X 默认为GET请求方式

------

- curl -X POST -H “superman285:xxx” https://www.baidu.com

------

- curl -X POST -v -H “superman285:xxx” https://www.baidu.com
- curl -X POST -v -s -H “superman285:xxx” https://www.baidu.com

请求内容为

> \> POST / HTTP/1.1
> \> Host: www.baidu.com
> \> User-Agent: curl/7.59.0
> \> Accept: \*/*
> \> superman285:xxx
>
> \>

响应内容为

> < HTTP/1.1 302 Found
> < Connection: Keep-Alive
> < Content-Length: 17931
> < Content-Type: text/html
> < Date: Mon, 03 Sep 2018 16:36:53 GMT
> < Etag: "54d97485-460b"
>
> \* Server bfe/1.0.8.18 is not blacklisted
> < Server: bfe/1.0.8.18
> <
> 网页html内容...



------

- curl -X POST -d "123456789" -v -s -H “superman285:xxx” https://www.baidu.com

请求内容为

> \> POST / HTTP/1.1
> \> Host: www.baidu.com
> \> User-Agent: curl/7.59.0
> \> Accept: \*/*
> \> superman285:xxx
>
> \> Content-Length: 9
>
> \> Content-Type: application/x-www-form-urlencoded
>
> \>
>
>
>
> 1234567890(请求正文)

响应内容为

> < HTTP/1.1 302 Found
> < Connection: Keep-Alive
> < Content-Length: 17931
> < Content-Type: text/html
> < Date: Mon, 03 Sep 2018 16:36:53 GMT
> < Etag: "54d97485-460b"
>
> \* Server bfe/1.0.8.18 is not blacklisted
> < Server: bfe/1.0.8.18
> <
> 网页html内容...(响应正文)

------

curl -O https://ss0.bdstatic.com/img/logo_baidu.com

------



:cyclone: **注意**

curl -s -v -H "superman285:666" https://xiedaimala.com/search?keyword=scss&type=courses&page=1

如果在命令行中输入这行命令，只能识别一个查询参数keyword=scss，解决方法：

- 转义： https://xiedaimala.com/search?keyword=scss\&type=courses\&page=1
- 加双引号：“https://xiedaimala.com/search?keyword=scss&type=courses&page=1”

> 参考博客园-mofy文章 https://www.cnblogs.com/z-books/p/6228284.html



#### 请求与响应的构成

------

> HTTP请求和HTTP响应都包含4部分



###### HTTP请求

1. 请求方法 路径 协议/版本号 (请求行)
2. 请求头
3. 空行
4. 请求数据正文

###### 请求具体格式

- 第1部分：动词 路径 协议/版本号
- 第2部分：key1:value1
- 第2部分：key2:value2
- 第2部分：key3:value3
- 第2部分：Content-Type
- 第2部分：Host
- 第2部分：User-Agent
- 第3部分：一个回车(\n)
- 第4部分：要上传的数据

请求最多包含4部分，最少包含3部分，第4部分**可为空**。



一般第二部分必须要有

Host(向谁发请求/请求的主机地址)、Accept(接收格式)



💡 **请求方法动词有：**

==GET== **|** ==POST== **|** ==PUT== **|** ==PATCH== **|** ==DELETE==



💡 路径默认为“/”,路径包括==查询参数==，但不包括==锚点==。

✔ /search?keyword=SASS&type=courses&page=1

× /search?keyword=SASS&type=courses&page=1#2



💡 第2部分Content-Type标注了第4部分内容的格式



💡 要上传的数据最常见的是用户名和密码



💡 查看第四部分内容在FormData或Request Payload中。



###### HTTP响应

1. 状态行 「协议/版本号 状态码 状态描述」
2. 响应头
3. 空行
4. 响应数据正文

###### 响应具体格式

- 第1部分：协议/版本号 状态码 状态解释

- 第2部分：key1: value1

- 第2部分：key2: value2

- 第2部分：Content-Length

- 第2部分：Content-Type(标注第4部分格式) 

  ```
  例如text/html | application/javascript | json | image/png
  ```

- 第3部分：一个回车(\n)

- 第4部分：要下载的内容



💡 第2部分Content-Type标注了第4部分内容的格式

💡 第2部分中的Content-Type遵循MIME规范



###### 状态码

- 1xx 不常用
- 2xx 表示成功
- 3xx 表示滚吧    301表示搬家了永远不回来了,302表示临时外出可能以后还回来
- 4xx 表示你错了  (**访问者**出错了，例如地址打错了等)
- 5xx 表示好吧,我错了    (**服务器**出错了，例如服务器挂了、服务器停止了) 

==状态码是服务器对浏览器说的话，需要记住。==



**常见状态**

- 200 OK 客户端请求成功
- 301：Move Permanently 永久重定向，请求的资源已被永久的移动到新URI
- 302：Found 临时重定向 与301类似，但资源只是临时被移动
- 400 Bad Request 客户端请求的语法错误，服务器无法理解
- 403 Forbidden 服务器拒绝执行此请求
- 404 Not Found 请求的资源不存在，例如，输入了错误的URL
- 500 Internal Server Error 服务器内部错误，无法完成请求
- 502 服务器网关错误，收到无效响应



请求和响应的第一部分都含有 `协议/版本号`

> **查看请求和响应的方法**

打开浏览器开发者工具Developer Tools，Network选项卡，右键添加Method页签(如果没有的话。)

Method页签看GET或POST方法，status看状态码，protocol看协议/版本号，type看类型可以为document、script、png等

![chrome-tab](chrome-tab.png)



然后在Name页签点一下你想要查看的URI

在右侧弹出页的Headers,有General、Response Headers、Request Headers等信息

![chrome-rr](chrome-rr.png)

General

> Request URL: https://xiedaimala.com/
>
> Request Method: GET
>
> Status Code: 200 OK
>
> Remote Address: 114.215.175.213:443
>
> Referrer Policy: no-referrer-when-downgrade

在Response Headers或Request Headers想查看响应或请求详情，关键来了：

`点击view source`

`点击view source`

`点击view source`

Response Headers/Request Headers**右侧浅色小字**

这儿可以==查看到请求|响应内容的前3部分==



:cyclone:请求的第4部分数据在Form Data或Request Payload中查看 [*GET请求没有第4部分数据*]



![chrome-request4](request-chrome.png)

------



:cyclone: 响应的第4部分数据在Preview或Response的tab中查看



![response-chrome4](response-chrome.png)

------

#### 