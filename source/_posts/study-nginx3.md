---
title: nginx初步使用（-）
date: 2018-05-08 20:06:30
tags: nginx
---

> 在本地用 brew install nginx 之后，迫不及待的想要使用一下

* mac 下默认监听 8080 端口，输入 localhost:8080 就可以看到 nginx 的欢迎页

## 更改默认配置文件

> 默认安装在/usr/local/etc/nginx 下面

* nginx.conf 是其默认配置文件，nginx -s reload 读取的就是这个
* 如果想配置多个域名，可以在 nginx 目录下新建一个 conf.d 文件夹，在 nginx.conf 里`include /usr/local/etc/nginx/conf.d/*.conf;`
* 域名配置文件一个最佳实践就是按照域名来写，比如 yoki.com.cn.conf
