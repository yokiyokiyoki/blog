---
title: nginx官方文档译文
date: 2018-01-14 13:16:04
tags: nginx
---

## 简介

nginx 有一个 master 进程和几个 worker 进程。master 进程的主要作用是读取和操作配置文件，同时维护 worker 进程。而真正处理请求的是 worker 进程。nginx 采用基于事件模型和系统依赖的机制最终将请求分配给 worker 进程。worker 进程的数量被定义在配置文件中，或者有可能被自动调整为 CPU 的核心数。

默认情况下，配置文件命名为 nginx.conf，放在/usr/local/nginx/conf，/etc/nginx，或者/usr/local/etc/nginx 目录下

## 启动，停止，reloading 配置

运行可执行文件即可开启 nginx。一旦 nginx 开启之后，它可以使用可执行的-s 参数进行控制。下面是其语法：

```javascript
nginx -s signal
```

信号(signal)可以是下面的其中一个：

* stop —— 快速关闭
* quit —— 优雅地关闭
* reload —— 重新加载配置文件
* reopen —— 重新打开日志文件

比如，等待所有 worker 进程服务完当前请求，然后关闭 nginx 进程：

```javascript
 nginx -s quit//这条命令，必须由开启nginx的同一个用户执行。
```

如果改变了配置文件，除非是 nginx 重启，否则可以执行下面的命令进行应用新的配置：

```javascript
 nginx -s reload
```

一旦 master 进程收到信号重新加载配置文件，它会检查新的配置文件的语法，并尝试应用配置。如果成功，master 进程开启新的 worker 进程，并且给旧的 worker 进程发送信息，请求它们关闭。否则，master 进程将会回滚修改并继续使用旧配置。

旧的 worker 进程，接受到命令关闭，将会停止接收新的请求，并继续服务完当前请求。自此之后，worker 进程将正式退出。

信号可以通过 unix 工具发送给 nginx 进程，例如 kill 命令。这种情况下，信号会直接发送给指定 pid 的进程。默认情况下，nginx 的 master 进程 ID 被写在/usr/local/nginx/logs 或/var/run 目录下的 nginx.pid 文件中。

比如，假如 master 进程的 ID 是 1628，那么发送 QUIT 信号给 nginx 会导致其优雅地退出：

```javascript
kill -s QUIT 1628
```

获取所有正在运行的 nginx 进程列表信息，可以执行：

```javascript
ps -ax | grep nginx
```

## 配置文件结构

nginx 由模块组成。这些模块都被配置文件中的指令所控制。指令包括简单指令和指令块。一条简单的指令由名字和参数组成，参数由空格隔开，以分号（;）结束。一个指令块的结构和简单指令的一样，但是，它以额外的指令集合结束，指令集用大括弧（{和}）包围。

如果一个指令块里面有其他的指令块，那么它被称为一个上下文（例如，events，http，server 和 location）。

配置文件中，那些位于任何上下文之外的指令都认为是在 main 上下文中。events 和 http 指令位于 main 上下文，server 位于 http 上下文之中，location 位于 server 之中。

一行位于#后面的被认为是注释。

## 静态内容

web 服务器一个重要的任务就是提供静态资源服务（例如图片等）。我们会实现一个例子，这个例子会根据请求，从不同的本地目录提供静态文件：/data/www（包含 html 文件），/data/images（包含图片）。这时候我们需要在 http 指令块里面创建一个 server 指令块，里面又包含两个 location 指令块。

首先，创建/data/www/index.html 文件，并放置一些图片于/data/images 目录下。

打开配置文件，默认的配置文件已经包含几个实例 server 指令块，大部分都被注释了。我们取消注释，并且开始一个新的 server 指令块：

```javascript
http {
	server {

	}
}
```

通常来说，配置文件会包含几个 server 指令块，它们由它们监听的端口号和服务器名称来区分。一旦 nginx 决定由哪一个 server 处理一个请求，它会把在请求 header 中的 URI 与在 location 指令块中定义的参数进行测试比较。

向 server 指令块添加 location 指令块：

```javascript
location / {
	root /data/www;
}
```

location 指令块指定了 URI”/”前缀。为了匹配请求，URI 将会被添加到 root 指令指定的路径，即是/data/www，实现了从路径到本地文件系统的转变。如果有多个匹配的 location 指令块，那么 nginx 将会选择那个拥有最长匹配前缀的。上面的 location 指令块提供了最短的前缀，所以如果其他所有 location 指令块都匹配失败，此指令块才生效。

下一步，添加第二个 location 指令块：

```javascript
location /images/ {
	root /data;
}
```

它会匹配以/images/开头的请求（虽然 location /也同时匹配此请求，但是却是更短的前缀匹配）。

最后 server 指令块的配置应该是这样的：

```javascript
server {
	location / {
		root /data/www;
	}

	location /images/ {
		root /data;
	}
}
```

这已经是一个服务器的配置文件了，这个服务器将会在本地机器http://localhost/监听80端口，并且是可访问状态。服务器将从路径/data/images发送文件去响应URIs以/images/开头的请求。比如，客户请求http://localhost/images/example.png，nginx将发送/data/images/example.png文件。如果该文件不存在，nginx将返回包含404 error 的响应。URIs 不是以/images/开头的请求将会被影射到/data/www 路径。例如，请求http://localhost/some/example.html，nginx将会发送/data/www/some/example.html文件。

为了应用新的配置文件，如果 nginx 还没有开启，开启即可；否则可以发送 reload 信号给 nginx 的 master 进程：

```javascript
nginx -s reload
```
