---
title: 使用shell脚本来自动部署项目到服务器
date: 2019-04-30 17:11:12
tags: 计算机基础
---

# 前言

我们可以通过shell远程执行一些命令，来达到我们部署系统的目的。这样我们就可以不用sftp可视化界面来拖动，省去了这一步。

# 远程执行

> 一般我们是通过ssh来登录服务器

## 简单的命令

```bash
ssh user@remoteNode "cd /home ; ls"
```
- 双引号，必须有。如果不加双引号，第二个ls命令在本地执行
- 分号，两个命令之间用分号隔开

## 复杂的命令

远程执行的内容在“<< eeooff ” 至“ eeooff ”之间，在远程机器上的操作就位于其中

```bash
ssh user@remoteNode > /dev/null 2>&1 << eeooff
cd /home
touch abcdefg.txt
exit
eeooff
echo done!
```
需要注意以下

- << eeooff，ssh后直到遇到eeooff这样的内容结束，eeooff可以随便修改成其他形式。
- 重定向目的在于不显示远程的输出了
- 在结束前，加exit退出远程节点

## 部署脚本

先使用tar 命令压缩文件，减少文件上传的大小，过去了服务器再解压

```bash
# 移除本地压缩文件
rm -rf dist.tar.gz

# 压缩文件，其中 dist为要上传的文件所在目录
tar -zcvf dist.tar.gz dist/

# 上传到服务器（需要输入密码，如果已经进行过私钥配置，则不用），其中/home/savoygu/gusaifei 为上传文件所在目录
scp  -r dist.tar.gz root@47.93.xxx.xxx:/home/savoygu/gusaifei

# 登录到服务器（需要输入密码，如果已经进行过私钥配置，则不用）
# 服务器环境开启
ssh root@47.93.xxx.xxx << EOF

# 进入目标目录
cd /home/savoygu/gusaifei
# 解压
sudo tar -zxvf dist.tar.gz --strip-components 1
# 移除线上压缩文件
sudo rm -rf 

exit
EOF
# 服务器环境结束
echo 上传完成！
```