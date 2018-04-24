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
