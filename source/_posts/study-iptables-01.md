---
title: iptables的设置
date: 2018-06-13 11:27:06
tags: 计算机基础
---

# 场景

* 在 vultr 买了一个服务器后，发现可以 ping 通
* 但是输入网址却无法连接
* 但是开启了 vpn 之后，可以上到这个服务器

# iptables 是什么

> 以下来自[维基百科](<https://wiki.archlinux.org/index.php/Iptables_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)>)

* iptables 是 linux 内核防火墙的命令行工具
* 通常也指代内核级防火墙
* iptables 用于 ipv4，ip6tables 用于 ipv6
* iptables 可以检测，修改，转发，重定向和丢弃 ipv4 数据包

# 基本概念

## 表（tables）

* 过滤 ipv4 数据包的代码已经内置于内核之中，并且按照不同的目的被组织成表的集合
* 表由一组预先定义的链组成，链包含遍历顺序规则
* 包含五张表，比较常用的就是 filter 表：用于存放于与防火墙相关操作的默认表

## 链(chain)

* 表由链组成
* 内核空间中：从一个网络接口进来，到另一个网络接口取得 PREROUTING（路由前）
* 数据包从内核流入用户空间的 INPUT （数据包流入口）
* 数据包从用户空间流出的 FORWARD （转发关卡）
* 进入/离开本机的外网接口 OUTPUT （数据包出口）
* 进入/离开本机的内网接口 POSTROUTING （路由后）

# 操作

* 去到所在文件，一般是在/etc/init.d/iptables
* 可以进行关闭(stop)，开启（start），重启（restart）
