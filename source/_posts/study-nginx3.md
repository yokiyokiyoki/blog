---
title: nginx初步使用（-）
date: 2018-05-08 20:06:30
tags: nginx
---

> 在本地用 brew install nginx 之后，迫不及待的想要使用一下

* mac 下默认监听 8080 端口，输入 localhost:8080 就可以看到 nginx 的欢迎页

## 更改默认配置文件

> 默认安装在/usr/local/etc/nginx 下面

### 位置

* nginx.conf 是其默认配置文件，nginx -s reload 读取的就是这个
* 如果想配置多个域名，可以在 nginx 目录下新建一个 sites-enabled 文件夹，然后再建一个以区分 http 和 https，在 nginx.conf 里`include /usr/local/etc/nginx/sites-enabled/*`
* 我看 centos 系统里新建的文件夹是 conf.d
* 域名配置文件一个最佳实践就是按照域名来写，比如 yoki.com.cn.conf

### 配置介绍

```javascript
# user字段表明了Nginx服务是由哪个用户哪个群组来负责维护进程的，默认是nobody
# 我这里用了liyoujie用户，staff组来启动并维护进程
# 查看当前用户命令： whoami
# 查看当前用户所属组命令： groups ，当前用户可能有多个所属组，选第一个即可
user liyoujie staff;

# worker_processes字段表示Nginx服务占用的内核数量
# 为了充分利用服务器性能你可以直接写你本机最高内核
# 查看本机最高内核数量命令： sysctl -n hw.ncpu
worker_processes 4;

# error_log字段表示Nginx错误日志记录的位置
# 模式选择：debug/info/notice/warn/error/crit
# 上面模式从左到右记录的信息从最详细到最少
error_log  /usr/local/var/logs/nginx/error.log debug;

# Nginx执行的进程id,默认配置文件是注释了
# 如果上面worker_processes的数量大于1那Nginx就会启动多个进程
# 而发信号的时候需要知道要向哪个进程发信息，不同进程有不同的pid，所以写进文件发信号比较简单
# 你只需要手动创建，比如我下面的位置： touch /usr/local/var/run/nginx.pid
pid  /usr/local/var/run/nginx.pid;

events {
    # 每一个worker进程能并发处理的最大连接数
    # 当作为反向代理服务器，计算公式为： `worker_processes * worker_connections / 4`
    # 当作为HTTP服务器时，公式是除以2
    worker_connections  2048;
}

http {
    # 关闭错误页面的nginx版本数字，提高安全性
    server_tokens off;
    include       mime.types;
    default_type  application/octet-stream;

    # 日志记录格式，如果关闭了access_log可以注释掉这段
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                 '$status $body_bytes_sent "$http_referer" '
    #                '"$http_user_agent" "$http_x_forwarded_for"';

    # 关闭access_log可以让读取磁盘IO操作更快
    # 当然如果你在学习的过程中可以打开方便查看Nginx的访问日志
    access_log off;

    sendfile        on;

    # 在一个数据包里发送所有头文件，而不是一个接一个的发送
    tcp_nopush     on;

    # 不要缓存
    tcp_nodelay on;

    keepalive_timeout  65;

    gzip  on;
    client_max_body_size 10m;
    client_body_buffer_size 128k;

    # 引入其他文件，一个是http服务的，一个是https服务的
    # 由这里include可以知道，那些被include的文件都是server上下文开始的（并不是http上下文）
    # default default-ssl
    include /usr/local/etc/nginx/sites-enabled/*;
}
```
