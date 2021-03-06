---
title: 浅谈http
date: 2018-04-23 19:39:38
tags: Http
---

> http（hypertext transfer protocol）超文本传输协议

## http 传输流

> 发送端在层与层间传输，每经过一层都会被加上首部信息，接受端每经过一层都会删除一条

* 应用层：本来是 http 数据
* 经过传输层，加上 tcp 首部
* 经过网络层，加上 ip 首部
* 经过链路层，加上以太网首部
* 然后接收端依次经过链路层，网络层，传输层，应用层

## 状态码

### 1xx 传达信息

* 100 continue
* 101 switching protocols

### 2xx 成功

* 200 OK，表示从客户端发来的请求在服务器端被正确处理
* 204 No content，表示请求成功，但响应报文不含实体的主体部分
* 206 Partial Content，进行范围请求

### 3xx 重定向

* 301 moved permanently，永久性重定向，表示资源已被分配了新的 URL
* 302 found，临时性重定向，表示资源临时被分配了新的 URL
* 303 see other，表示资源存在着另一个 URL，应使用 GET 方法获取资源
* 304 not modified，表示服务器允许访问资源，但因发生请求未满足条件的情况
* 307 temporary redirect，临时重定向，和 302 含义相同

### 4XX 客户端错误

* 400 bad request，请求报文存在语法错误
* 401 unauthorized，表示发送的请求需要有通过 HTTP 认证的认证信息
* 403 forbidden，表示对请求资源的访问被服务器拒绝
* 404 not found，表示在服务器上没有找到请求的资源
* 405 method not allowed 一般是 get 和 post 弄错了

### 5xx 服务器错误

* 500 internal sever error，表示服务器端在执行请求时发生了错误
* 503 service unavailable，表明服务器暂时处于超负载或正在停机维护，无法处理请求
* 504 gateway timeout 网关超时

## http 协议格式

> http 的请求和响应的消息协议是一样的，分为三个部分，起始行，消息头和消息体。这三个部分以 CRLF 作为分隔符，最后一个消息头有两个分隔符，表示消息头部结束。

* 起始行：如 GET /index.html HTTP/1.1
* 消息头部（http header）：多个键值对组成，如 Content-Encoding: gzip，多个键值对之间使用 CRLF 作为分隔符
* 消息体（http body）：实体部分，请求实体和响应实体

## http 的无状态性

* 所谓 HTTP 协议的无状态性是指服务器的协议层无需为不同的请求之间建立任何相关关系，它特指的是协议层的无状态性。但是这并不代表建立在 HTTP 协议之上的应用程序就无法维持状态。应用层可以通过会话 Session 来跟踪用户请求之间的相关性，服务器会为每个会话对象绑定一个唯一的会话 ID，浏览器可以将会话 ID 记录在本地缓存 LocalStorage 或者 Cookie，在后续的请求都带上这个会话 ID，服务器就可以为每个请求找到相应的会话状态。

## 输入 url 到页面加载发生了什么

* 输入地址
* 浏览器查找域名的 IP 地址
* 这一步包括 DNS 具体的查找过程，包括：浏览器缓存->系统缓存->路由器缓存...
* 浏览器向 web 服务器发送一个 HTTP 请求
* 服务器的永久重定向响应（从 http://example.com 到 http://www.example.com）
* 浏览器跟踪重定向地址
* 服务器处理请求
* 服务器返回一个 HTTP 响应
* 浏览器显示 HTML
* 浏览器发送请求获取嵌入在 HTML 中的资源（如图片、音频、视频、CSS、JS 等等）
* 浏览器发送异步请求
