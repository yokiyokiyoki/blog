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
rpm -qa | grep mysql 查看有哪些安装包
yum remove mysql mysql-server mysql-libs compat-mysql51 注意这个卸载不干净
rm -rf /var/lib/mysql
rm /etc/my.cnf
rpm -qa|grep mysql 再看下有没有删完
whereis mysql 查看残留的目录、如有则删除
```

- 如果发现有安装包，可以用 rpm -e 命令删掉安装包

#### 下载 Mysql 的 repo 源

```javascript
mkdi /usr/local/download
cd /usr/local/download/
wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm
```

#### 安装 mysql-community-release-el7-5.noarch.rpm 包

```javascript
rpm -ivh mysql-community-release-el7-5.noarch.rpm //rpm -ivh是安装命令的意思
```

#### 安装 mysql

- yum install -y mysql-community-server

#### 安装成功后重启 MySQL 服务

- service mysqld restart

#### 查看 MySQL 服务进程和端口

```javascript
ps -ef | grep mysql
netstat -tunpl | grep 3306
```

- ps 命令是 Process Status 的缩写
- ps 命令用来列出系统中当前运行的那些进程。ps 命令列出的是当前那些进程的快照，就是执行 ps 命令的那个时刻的那些进程，如果想要动态的显示进程信息，就可以使用 top 命令。[改命令详情实例查看这里](https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/ps.html#id7)
- netstat 是显示各种网络信息的工具

#### 获取 mysql 默认密码

- grep 'temporary password' /var/log/mysqld.log
- 如果没有反应，需要重置密码

```javascript
mysql -u root
use mysql;
update user set password=password('123456') where user='root';
delete from user where user='';//删除空用户
systemctl restart mysql//重启mysql
```

#### 需要远程连接

- 开放 3306 端口

```javascript
systemctl start firewalld.service //启动防火墙
firewall-cmd --permanent --add-port=3306/tcp
firewall-cmd --reload
```

- 开启远程登录

```javascript
mysql -u root -p
use mysql;
update user set host='%' where user='root' and host='localhost';
exit；
systemctl restart mysql//重启mysql
```

## 阿里云远程连接的坑

- 在外面怎么连接 mysql 各种超时，即使把防火墙关了也无济于事。是因为阿里云有个[安全组策略](https://help.aliyun.com/document_detail/25471.html)，
- [在安全组策略配置 3306 端口开放](https://blog.csdn.net/u010955892/article/details/72774920)
