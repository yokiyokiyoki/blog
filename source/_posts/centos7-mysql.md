---
title: 阿里云centos7的mysql安装与启动
date: 2018-08-26 16:07:52
tags: mysql
---

## 背景

- 需要给人开发一个项目，然后我让客户自己选购了阿里云的 centos7 系统（他才买了 350 元一年，我很好奇为什么这么便宜，我是 680...杀熟嘛）
- 然后让客户用 ssh 工具配置，这样我就可以通过 ssh 来连接他的服务器了
- 这个时候我就需要安装 mysql，并在外面来远程连接了

## 安装 mysql

#### 安装依赖包

```javascript
yum -y install gcc gcc-c++ ncurses ncurses-devel cmake bison
```

###### yum 和 rpm 是什么

- yum，是 Yellow dog Updater Modified 的简称，起初是由 yellow dog 这一发行版的开发者 Terra Soft 研发，用 python 写成，那时还叫做 yup(yellow dog updater)，后经杜克大学的 Linux@Duke 开发团队进行改进，遂有此名。
- yum 的宗旨是自动化地升级，安装/移除 rpm 包，收集 rpm 包的相关信息，检查依赖性并自动提示用户解决。
- rpm（二进制包）是 linux 的可执行程序，类似 windows 的 exe 文件

#### 先检测系统是否自带原有版本 mysql 安装包，如果有要先卸载删除，不然不能成功安装和启动

```javascript
    [root@root ~]# rpm -qa | grep mysql 查看有哪些安装包，如没有直接进入第3、4
    [root@root ~]# yum remove mysql mysql-server mysql-libs compat-mysql51 注意这个卸载不干净
    [root@root ~]# rm -rf /var/lib/mysql
    [root@root ~]# rm /etc/my.cnf
    [root@root ~]# rpm -qa|grep mysql 再看下有没有删完
    [root@root ~]# whereis mysql 查看残留的目录、如有则删除
```
