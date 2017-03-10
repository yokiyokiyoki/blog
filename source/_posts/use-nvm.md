---
title: 使用nvm
date: 2017-03-10 11:04:09
tags: "node"
---

# Node Version Manager（node版本管理）

## 使用nvm安装和管理node
> node版本更新很快，为了能在版本之间迅速切换，我们要使用nvm来安装和管理。

#### 安装nvm

我们可以从[Github](https://github.com/coreybutler/nvm-windows)下载
> 验证安装，打开控制台，输入`nvm`,看是否输出所有命令

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