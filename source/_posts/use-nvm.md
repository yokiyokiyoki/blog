---
title: 使用nvm
date: 2017-03-10 11:04:09
tags: "node"
comments: false
---

# Node Version Manager（node版本管理）

## 使用nvm安装和管理node
> node版本更新很快，为了能在版本之间迅速切换，我们要使用nvm来安装和管理。<br>
用nvm下载了相应的node也会下载相应的npm进行管理

#### 安装nvm

##### windows

我们可以从[Github](https://github.com/coreybutler/nvm-windows)下载

默认安装路径是`C:\Users\Administrator\AppData\Roaming\nvm`，存放我们下载的各个版本的node
> 验证安装，打开控制台，输入`nvm`,看是否输出所有命令

##### centos7

- 下载
```bash
#首先确保centos已安装git,以确保之后nvm本身的升级
curl https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash
```
- 重启终端
- 升级nvm
```bash
[root@localhost .nvm]# nvm --version
0.30.2
[root@localhost .nvm]# git fetch -p
[root@localhost .nvm]# git rev-list --tags --max-count=1
0a95e77000515c1156be593642dd4e452f2f098e
[root@localhost .nvm]# git describe --tags 0a95e77000515c1156be593642dd4e452f2f098e
v0.33.2
[root@localhost .nvm]# git describe --abbrev=0 --tags
v0.33.2
[root@localhost .nvm]# git checkout $(git describe --tags `git rev-list --tags --max-count=1`)
之前的 HEAD 位置是 7f3145b... [New] add support for `$NVM_DIR/default-packages` file
HEAD 目前位于 0a95e77... v0.33.2
[root@localhost .nvm]# source ~/.nvm/nvm.sh
[root@localhost .nvm]# nvm --version
0.33.2
```


#### 命令
- nvm list   #查看本地所有node版本
- nvm install 4.2.2   #安装 4.2.2 版本
- nvm use 4.2.2    #切换至 4.2.2 版本
- nvm uninstall 4.2.2    #卸载4.2.2 版本

#### 镜像
> nvm默认的下载地址是`http://nodejs.org/dist/`,这是国外的服务器，在国内下载速度很慢。

- 需要把下列代码复制到你nvm的安装路径下的setting.txt
```bash
node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```