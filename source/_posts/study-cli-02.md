---
title: 前端如何搭建一个成熟的脚手架
date: 2019-03-25 15:20:12
tags: 前端
---

## 前言

有了之前的基础，我们现在可以讲讲一个成熟的脚手架是怎么做了。vue-cli作为vue的脚手架，给如此多的前端开发者使用，已经算是成熟了吧。
这里我们参考vue-cli的源码，基于rollup和typescript一步步搭建。

## 开始

> 以下我们的命令仍然是ds~,模板是ds-cli-lib-template

### 目录结构
```
├─ bin            # 打包文件目录
│  ├─ ds.js       # package.json里的bin字段引用文件 
├─ src
│  ├─ lib         # 具体命令目录
│     ├─ list     # ds list
│     ├─ init     # ds init
│  ├─ utils       # 工具函数
├─ main.ts        # 入口文件
├─ typings        # typescript类型文件目录
├─ rullup.config.js # rollpu构建配置
├── test            #  测试用例
```

### 编写构建配置

现如今，webpack用来开发应用（热更新hmr,代码拆分等）,rollup用来开发类库（简单易上手，打包后代码能读懂，至于其他的特性webpack基本已支持）。
现在来明确我们的需求

- 使用typescript编写模块代码
- 打包成umd模块规范的代码
- 可以引用commonjs规范的包（因为历史原因，大多数包都不是ES模块规范）
- 压缩打包代码，减少体积

```javascript
//rollup.config.js
import typescript from "rollup-plugin-typescript2";
import commonjs from 'rollup-plugin-commonjs'
import { uglify } from 'rollup-plugin-uglify'
export default {
    //入口文件
  input: "src/main.ts",
  output: [
    {
      banner: "#!/usr/bin/env node",
      /**
       * 头部插入这段代码
       * */
      name: "ds",
      file: "bin/ds.js",
      //打包成umd模块规范
      format: "umd"
    }
  ],
  plugins: [
    typescript(),
    commonjs({
      include: "node_modules/**",
      extensions: ['.js', '.ts']
    }),
    uglify()
  ],
};
```
npm脚本命令("scripts"字段)
```json
{
"clean": "rm -rf ./bin && mkdir bin",
"build": "npm run clean && rollup --config"
}
```

### 编写入口文件

> 是一些非常基础的东西，我们一般不放很复杂的逻辑在入口文件里。

```typescript
const cmd = require('commander');
const config = require('../package.json');

//这里cli-init.ts和cli-list.ts我们可以简单导出一个函数，如
// export default function(...args) {
//     console.log('init')
// }
import init from './lib/init/cli-init'; 
import list from './lib/list/cli-list';

const command= {
    init,
    list
};

//map对应的type，从而执行
function exec(type, ...args) {
    config.debug = args[0].debug;
    command[type](...args);
}

cmd
  .usage('<command>')
  .version(config.version)
  .description('欢迎使用ds-cli');

cmd
  .command('init')
  .description('初始化组件模板')
  .action((...args) => exec('init', ...args));

cmd
  .command('list')
  .description('查看线上组件模板')
  .action((...args) => exec('list', ...args));

cmd.command('help')
  .description('查看帮助')
  .action(() => cmd.help());

// 解析输入的参数
cmd.parse(process.argv);
if (!cmd.args.length) {

  cmd.help();
}
```
我们打包到bin文件夹下后，配置一下package.json的bin字段为bin/ds.js，然后发布npm试一下命令。如果失败，请重新审视上述流程。

### 初始化模板

#### 我们对模板的要求

- 文件目录必须含有template文件夹，并且所需模板文件放在该目录下
- 文件名命名规范是ds-cli-‘name’-template，方便脚手架拉取
- 可用meta.js提高自定义程度（所谓动态化模板）

#### 期望命令
ds init <template-name> <app-name>

#### 流程

```javascript
if(当前目录下构建){
    询问一下是不是当前目录,是的话进入run函数
}else{
    进入主流程run函数
}
//run函数
function run(){
    if(模板路径是本地文件路径){
        //支持本地模板如ds init /usr/webpack test
        if(路径存在){
            //动态构造模板到你的目录如test
            generate()
        }else{
            //报错日志
        }
    }else{
        //1.检查当前process的node版本，大于6才可以用
        //2.检查当前package.json的版本，跟远程仓库的版本比较一下。如果不一样,就提醒一下用户有新版本
        //3.下载远程仓库到本地(本地一般存放用户目录里的.ds-template文件夹下)，然后执行generate函数
    }
}
```

#### 动态模板

> 大家也看到了，其实最重要的就是generate函数~

- 而如果我们去掉这一步generate模板的话，其实就是相当于下载一个静态模板，如果我们对于用户自定义没要求的话，其实可以跳过这一步。
- generate函数里面用到了metalsmith，这个就相当于我们之前用的gulp，通过不断地编写中间件来优化打包后的结果。
```javascript
function generate(){
    const opts = getOptions(name, templatePath) as meta;  // 获取meta.js配置,存到opts里
    // 我们把所需文件放在源文件的template目录下，其他一些如测试放在外面。初始化一下metalsmith
    const metalsmith = Metalsmith(path.join(templatePath, 'template')) //我们约定，将模板所有文件放在ds-cli-lib-template/template里
    //中间件
     metalsmith.use(askQuestions(opts.prompts))  // 询问问题，将信息存metalsmith.metadata()
    .use(filterFiles(opts.filters))  // 通过问题交互过滤掉不需要的文件
    .use(renderTemplateFiles()); // 模板里面可以使用handlebar语法，作为占位符，我们这里重新渲染模板文件

    // 源目录打包到目标目录to
    metalsmith.clean(false)
    .source('.') 
    .destination(to)
    .build((err, files) => {
      done(err);
    });
}
```
- 我们在模板（如ds-cli-lib-template）目录下需要构造meta.js，自定义我们所需的字段
```javascript
module.exports={
    //会通过中间件把这些字段存在metalsmith.metadata()，方便接下来的中间件调用
    prompts:{
        //格式可以参考https://github.com/SBoudrias/Inquirer.js/#question
        name: {
            type: 'string',
            required: true,
            message: 'Project name',
        },
        author: {
            type: 'string',
            message: 'Author',
        },
        description: {
            type: 'string',
            required: false,
            message: 'Project description',
            default: '构建一个lib',
        },
        lint: {
            "type": "confirm",
            "message": "是否用tslint"
        },
    },
    
    filters: {
        //当上面prompts的lint为false的时候，就过滤掉文件
        "tslint.json": "lint",
        "tsconfig.json": "lint"
    }
}
```
