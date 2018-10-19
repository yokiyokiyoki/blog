---
title: commit规范
date: 2018-10-19 16:20:54
tags: git
---

# 背景

> 我们团队采用 [Angular 的提交规范](https://github.com/isLishude/blog/issues/69)，有方便的插件，可放心食用。

- 对 git commit -m "不知道提交些什么"——这样的 commit 说拒绝。
- 生成易读的 commit，可以方便回退版本以及 code-review。

# 使用说明

### 第一种方式（并非自定义）

- 确保全局安装或者该项目已经有了 commitizen ，命令：npm i -g commitizen / npm i -D commitizen
- 查看 package.json，看看是否有 config 选项，并且里面 commitizen 选项，如果有则跳过第三个步骤。
- 安装 `adapter` 配置 commit message，例如，要使用 Angular 的 commit message 格式，可以安装 `cz-conventional-changelog`，执行`commitizen init cz-conventional-changelog --save --save-exact`
- `git add`以后提交使用`git cz`

# git hook 检查

> 鉴于 vscode 有很方便的提交 git 的图形化按钮，有些童鞋可能会一不小心就提交了自己的信息

- git 拥有提交工作流钩子（git hooks），我们在本地用 commit-msg 钩子搞事情～

### commit-msg 钩子

##### 简介

- 它会在用户输入提交信息之后被调用。这适合用来提醒开发者他们的提交信息不符合团队的规范。

##### 使用

- 为了简化使用 git hook，这里直接使用了 husky 插件，让 hook 使用更简单～（npm i husky -D）
- 使用符合 angular 提交规范的检查流（npm i validate-commit-msg -D）
- 在 package.json 的 scripts 里面加上"commitmsg": "validate-commit-msg"
