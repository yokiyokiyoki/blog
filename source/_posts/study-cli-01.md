---
title: 前端如何搭建一个的简单脚手架
date: 2019-02-18 20:34:44
tags: 前端
---

## 前言

这里先简单了解一下基础知识，接下来会基于typescript和rollup参考写一个较为完整的脚手架（动态模板），以下只能拉取静态模板。

## 基础

#### 脚手架发布和安装

> 这里假设我们的脚手架名字是ds-cli,以下都用这个名字。我们在命令行使用脚手架命令为ds

- 我们怎么样让用户通过npm或者yarn全局安装ds-cli之后，在终端能执行ds命令呢？答案就在package.json的bin字段
- 当我们bin字段指向我们的目标文件main.js,同时目标文件开头具有
```javascript
//这里是main.js
#!/usr/bin/env node

// --这种用法是为了防止操作系统用户没有将node装在默认的/usr/bin路径里。当系统看到这一行的时候，
// 首先会到env设置里查找node的安装路径，再调用对应路径下的解释器程序完成操作。
//这里是package.json
"bin": {
    "ds": "./main.js"
}
```
- 这样当我们发布上npm，别人下载下来后，就可以直接使用ds命令了。

#### 解析命令行

- 如何轻松便捷的读取命令行里的参数呢？这里我们使用tj大神的commander
```javascript
const cmd = require("commander");

// 比如我们想执行ds init **的命令，想出现“初始化组件模板”的描述
// action是执行这个命令后续的回调，...args是后面**的参数
cmd
  .command('init')
  .description('初始化组件模板')
  .action((...args) => {});

//解析命令行
cmd.parse(process.argv);
```

#### 用户交互

> 我们通过询问用户来获得一定的交互，这样可以知道用户需要什么

- 采用inquirer询问项目描述，作者
```javascript
//比如我们在上面那个action里面搞事情,即ds init之后问用户
...action((...args)=>{
    inquirer
      .prompt([
        {
          name: "description",
          message: "请输入项目描述"
        },
        {
          name: "author",
          message: "请输入作者名称"
        }
      ]).then(answers=>{
          //在这里获得上面的答案
          console.log(answers.description,answers.author)
      })
})
```

#### 拉取远程的仓库

- download-git-repo可拉取远程仓库

```javascript
const download = require("download-git-repo");
// 第一个git地址，第二个name是git clone下来后的名字...
download(
    "https://github.com/yokiyokiyoki/vue-fullpage.git#master",
    name,
    { clone: true },
    err => {
    ...
    }
);
```

#### 简单的模板替换
- 我们通过询问交互后，肯定内部做了些改变。这里我们可以把package.json的作者和描述简单改了
- 可以使用handbars，模板语法简单
```javascript
//这个是通过download-git-repo拉下来的package.json
{
    "author":"{{author}}",
    "description":"{{description}}"
}
```
- 然后我们通过读取文件字符串给handbars编译一下拿到的变量，再写入
```javascript
//通过询问拿到的answers
const meta = {
    name,
    description: answers.description,
    author: answers.author
};
const fileName = `${name}/package.json`;
//判断一下是否有这个文件
if (fs.existsSync(fileName)) {
    const content = fs.readFileSync(fileName).toString();
    const result = handlebars.compile(content)(meta);
    fs.writeFileSync(fileName, result);
}
```

#### 终端的一些效果

- 高亮终端打印出来的信息:chalk。如chalk.green('成功了')，chalk.red('失败了')
- 终端加载效果：ora,有个loading效果
```javascript
const spinner = ora("正在下载模板...");
spinner.start();
spinner.succeed();
```
- 打印日志的特殊标志：log-symbols

## 源码

```javascript
#!/usr/bin/env node

const cmd = require("commander");
const download = require("download-git-repo");
const inquirer = require("inquirer");
const handlebars = require("handlebars");
const fs = require("fs");
const ora = require("ora");
const chalk = require("chalk");
const symbols = require("log-symbols");

cmd
  .version("0.0.1", "-v, --version")
  .command("init <name>")
  .action(name => {
    if (fs.existsSync(name)) {
      // 错误提示项目已存在，避免覆盖原有项目
      console.log(symbols.error, chalk.red("项目已存在"));
      return;
    }
    inquirer
      .prompt([
        {
          name: "description",
          message: "请输入项目描述"
        },
        {
          name: "author",
          message: "请输入作者名称"
        }
      ])
      .then(answers => {
        download(
          "https://git.datatub.com:Uranus/general-template#master",
          name,
          { clone: true },
          err => {
            const spinner = ora("正在下载模板...");
            spinner.start();
            if (!err) {
              spinner.succeed();
              const meta = {
                name,
                description: answers.description,
                author: answers.author
              };
              const fileName = `${name}/package.json`;
              if (fs.existsSync(fileName)) {
                const content = fs.readFileSync(fileName).toString();
                const result = handlebars.compile(content)(meta);
                fs.writeFileSync(fileName, result);
              }
              console.log(symbols.success, chalk.green("项目初始化完成"));
            } else {
              spinner.fail();
              console.log(symbols.error, chalk.red(`拉取远程仓库失败${err}`));
            }
          }
        );
      });
  });
//解析命令行
cmd.parse(process.argv);

```
- 上面是main.js文件，然后修改一下package.json的字段（bin字段修改，模板变量）
- 发布npm，自己下载下来体验一下