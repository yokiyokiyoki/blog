---
title: 工作中常用git命令
date: 2017-02-13 15:35:34
tags: git
---

## 基础命令

* git clone 克隆仓库

* git checkout 切换分支

* git add 添加文件

* git commit 提交改动

* git push 推送本地分支到远端

* git merge 把某个分支的代码合并到当前分支

* git pull = git fetch + git merge 把远端分支更新到本地

* git status 当前分支状态

> 我一般习惯配置别名 alias，比如 merge 变成 mg，这样少打几个字母

## 常用命令组合

1.丢掉本地未提交改动

git checkout -f

2.丢掉本地已提交但未推送到远端的提交

git reset --hard origin/xxx

## 测试和发布

> 内部使用 gitlab

* master 主分支：用于外网线上发布，永远保持跟线上一致
* develop 分支：用于发布测试环境

### 日常开发说明

> 详情参见[git flow](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)

* 日常开发从 master 拉一个分支 feature-xxx 分支到本地
* 要发布测试环境，直接 mg 到 develop，然后发布
* 要发布线上，直接 mg 到 master，然后发布
* 日常紧急 bug 修复，可拉一个 hotfix 分支
