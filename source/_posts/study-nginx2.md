---
title: nginx简单使用
date: 2018-01-20 13:41:38
tags: nginx
---

> 前情提要：由于公司也是使用 nignx 解决前后端分离跨域问题，这里简单学习一下

## 设置简单的代理服务器

设置一个代理服务器，它即是一个用来接收请求，并传递它们到代理服务器，取回响应并发送响应给客户端的服务器。

我们将会配置一个简单的代理服务器，它将用本地文件来提供图片请求，而把其他请求转发到代理服务器。这个例子中，这两个服务器都将被定义在一个 nginx 实例中。

首先，在上面配置的基础上再添加一个 server 指令块到 nginx 的配置文件中：

```javascript
server {
	listen 8080;
	root /data/upl;

	location / {

	}
}
```

这是一个监听在 8080 端口（location 指令没有指定，默认会使用 80 端口），并且会映射所有请求到本地路径/data/upl 的简单服务器。创建此路径并在里面创建 index.html 文件。注意 root 指令要放在 server 上下文中。这种 root 指令将会在没有自己 root 指令的 location 指令块被选中来处理请求时应用。

下一步，使用上一节的服务器配置，并且修改其为一个代理服务器配置。在第一个 location 指令块，放置 proxy_pass 指令，将代理服务器的地址作为参数（包括代理服务器的协议，域名和端口号）。例子中，它是http://localhost:8080：

```javascript
server {
	location / {
		proxy_pass http://localhost:8080;
	}

	location /images/ {
		root /data;
	}
}
```

第二个 location 指令块目前指定的是/images/前缀到/data/images 路径的映射，为了使其能根据文件后缀匹配相应图片的请求，我们修改之：

```javascript
location ~ \.(gif|jpg|png)$ {
	root /data/images;
}
```

这里参数是一个匹配哪些以.gif，.jpg 或者.png 结尾的 URIs 的正则表达式。正则表达式前面需要放置~。相应的请求将会被影射到/data/images 路径。

当 nginx 选择一个 location 指令块去服务一个请求，它首先检查 location 指令指定的前缀（并记住此最长前缀），然后检查正则表达式。如果有一个正则表达式匹配了，nginx 选中此 location，否则，它将会应用前一个记住的 location。

最后，代理服务器的配置将会是这样：

```javascript
server {
	location / {
		proxy_pass http://localhost:8080;
	}

	location ~ \.(gif|jpg|png)$ {
		root /data/images;
	}
}
```

此服务器将会过滤那些以.gif，.jpg 或.png 结尾的请求，并且影射它们到/data/images 目录。传递其他所有请求到上面配置的代理服务上去。

## location

* = 开头表示精确匹配
* ^~ 开头表示 uri 以某个常规字符串开头，不是正则匹配
* ~ 开头表示区分大小写的正则匹配;
* ~\* 开头表示不区分大小写的正则匹配
* / 通用匹配, 如果没有其它匹配,任何请求都会匹配到

顺序 no 优先级：

(location =) > (location 完整路径) > (location ^~ 路径) > (location ~,~\* 正则顺序) > (location 部分起始路径) > (/)

```javascirpt
#直接匹配网站根，通过域名访问网站首页比较频繁，使用这个会加速处理
#这里是直接转发给后端应用服务器了，也可以是一个静态首页
# 第一个必选规则
location = / {
    proxy_pass http://tomcat:8080/index
}
# 第二个必选规则是处理静态文件请求，这是nginx作为http服务器的强项
# 有两种配置模式，目录匹配或后缀匹配,任选其一或搭配使用
location ^~ /static/ {
    root /webroot/static/;
}
location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {
    root /webroot/res/;
}
#第三个规则就是通用规则，用来转发动态请求到后端应用服务器
#非静态文件请求就默认是动态请求，自己根据实际把握
#毕竟目前的一些框架的流行，带.php,.jsp后缀的情况很少了
location / {
    proxy_pass http://tomcat:8080/
}
```
