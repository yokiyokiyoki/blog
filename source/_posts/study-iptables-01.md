---
title: 通过一个场景了解iptables和socks5协议
date: 2018-06-13 11:27:06
tags: 计算机基础
---

# 场景

* 在 vultr 买了一个服务器后，发现可以 ping 通
* 但是输入网址却无法连接
* 但是开启了 ss 之后，可以连接到这个服务器

### 解决办法

* 一开始以为是墙的问题，可以封禁端口，导致 80 端口不能使用
* 但是我换了其他端口也无法连接
* 后来用 wireshark 抓包，发现返回 icmp 协议： host administratively prohibited
* 谷歌后查发现是服务器防火墙没有开放 80 端口的缘故
* 那么另外一个问题随之而来呢，为什么通过 ss 之后就可以连接到服务器呢
* ss 服务端采用了将客户端传过来的用 socks5 协议封装，发到真正的目的地
* 换句话 socks5 协议穿透了防火墙，可以查看[这里](https://segmentfault.com/a/1190000011862912#articleHeader2)

# SOCK5 协议

> 以下来自[维基百科](https://zh.wikipedia.org/wiki/SOCKS)

* SOCKS 是一种网络传输协议，主要用于客户端与外网服务器之间通讯的中间传递。SOCKS 是"SOCKetS"的缩写

# iptables

> 以下来自[维基百科](<https://wiki.archlinux.org/index.php/Iptables_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)>)

* iptables 是 linux 内核防火墙的命令行工具
* 通常也指代内核级防火墙
* iptables 用于 ipv4，ip6tables 用于 ipv6
* iptables 可以检测，修改，转发，重定向和丢弃 ipv4 数据包

## 基本概念

### 表（tables）

* 过滤 ipv4 数据包的代码已经内置于内核之中，并且按照不同的目的被组织成表的集合
* 表由一组预先定义的链组成，链包含遍历顺序规则
* 包含五张表，比较常用的就是 filter 表：用于存放于与防火墙相关操作的默认表

### 链(chain)

* 表由链组成
* 内核空间中：从一个网络接口进来，到另一个网络接口取得 PREROUTING（路由前）
* 数据包从内核流入用户空间的 INPUT （数据包流入口）
* 数据包从用户空间流出的 FORWARD （转发关卡）
* 进入/离开本机的外网接口 OUTPUT （数据包出口）
* 进入/离开本机的内网接口 POSTROUTING （路由后）

## 操作

* 去到所在文件，一般是在/etc/init.d/iptables
* 可以进行关闭(stop)，开启（start），重启（restart）

# 推荐阅读

* [iptables 深入理解](http://www.cnblogs.com/lvxiaobo616/p/5715739.html)
