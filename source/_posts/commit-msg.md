---
title: commit规范
date: 2018-10-19 16:20:54
tags: git
---

# 背景

> 团队如果采用 [Angular 的提交规范](https://github.com/isLishude/blog/issues/69)，有方便的插件，可放心食用。

- 对 git commit -m "不知道提交些什么"——这样的 commit 说拒绝。
- 生成易读的 commit，可以方便回退版本以及 code-review。

# 使用说明

### 第一种方式（并非自定义,使用 ng 团队）

> 无法定义自己的模块/组件（scopes）,比如项目具体业务的页面

- 确保全局安装或者该项目已经有了 commitizen ，命令：npm i -g commitizen / npm i -D commitizen
- 查看 package.json，看看是否有 config 选项，并且里面 commitizen 选项，如果有则跳过第三个步骤。
- 安装 `adapter` 配置 commit message，例如，要使用 Angular 的 commit message 格式，可以安装 `cz-conventional-changelog`，执行`commitizen init cz-conventional-changelog --save --save-exact`
- `git commit`以后提交使用`git cz`

### 第二钟方式

> 自定义程度较高，可以有自己想要的模块

- 确保全局安装或者该项目已经有了 commitizen 和 cz-customizable
- 查看 package.json，看看是否有 config 选项，并且里面 commitizen 选项，如果有则跳过第三个步骤。
- 该项目下手动新建`.cz-config.js`文件，如下

```javascript
"use strict";
module.exports = {
  // 不要更改types, 只允许出现这几种
  types: [
    {
      value: "wip",
      name: "wip：大版本功能开发阶段性提交"
    },
    { value: "feat", name: "feat：完整新功能提交" },
    { value: "fix", name: "fix：修复bug" },
    {
      value: "build",
      name: "build：打包，准备发布"
    },
    { value: "docs", name: "docs：只修改了文档相关的文件，例如修改README.md" },
    {
      value: "style",
      name:
        "style：代码风格、不影响代码功能的更改，例如修改空格缩进，换行规范等n"
    },
    {
      value: "refactor",
      name: "refactor：既不修复错误也不添加新功能的代码更改，例如重构"
    },
    {
      value: "chore",
      name:
        "chore：对非业务性代码进行修改，例如包管理器,构建过程或辅助工具的变动"
    }
  ],
  // 按照项目模块, 自行配置
  scopes: [{ name: "品牌监测" }, { name: "活动监测" }, { name: "kol监测" }],
  // 可以根据匹配的类型不同, 显示不一样的scope,
  scopeOverrides: {
    fix: [{ name: "merge" }],
    chore: []
  },
  allowCustomScopes: true,
  //重要的改动要声明
  allowBreakingChanges: ["feat", "fix"]
};
```

- 在 package.json 下面添加

```javascript
{
  "config": {
    "commitizen": {
      "path": "node_modules/cz-customizable"
    }
  }
}
```

- `git commit`以后提交使用`git cz`

# git hook 检查

> 鉴于 vscode 有很方便的提交 git 的图形化按钮，有些童鞋可能会一不小心就提交了自己的信息

- git 拥有提交工作流钩子（git hooks），我们在本地用 commit-msg 钩子搞事情～

### commit-msg 钩子

#### 简介

- 它会在用户输入提交信息之后被调用。这适合用来提醒开发者他们的提交信息不符合团队的规范。

#### 使用

- 为了简化使用 git hook，这里直接使用了 husky 插件（继承了 git hook 所有钩子），让 hook 使用更简单～（npm i husky -D）

##### 使用 ng 的提交规范

- 安装`@commitlint/config-conventional`和`@commitlint/cli`（检查）
- - 新建`.commitlintrc.js`,如下

```javascript
"use strict";
module.exports = {
  extends: ["@commitlint/config-conventional"],
  rules: {}
};
```

#### 针对自定义的 Adapter

- 需要安装 `commitlint-config-cz`(自定义) 和`@commitlint/cli`（检查）
- 新建`.commitlintrc.js`,如下

```javascript
"use strict";
module.exports = {
  extends: ["cz"],
  rules: {}
};
```

- package.json 里面添加

```javascript
"husky": {
    "hooks": {
      "commit-msg": "commitlint -c -e $GIT_PARAMS"
    }
  }
```

### 自动生成 CHANGELOG

- 我们可以借助 `standard-version`(很多特性)这样的工具, 自动生成 CHANGELOG
- npm i -S standard-version
- package.json 里面添加

```javascript
"script": {
    "release": "standard-version"
}
```

- npm run release 生成 CHANGELOG
