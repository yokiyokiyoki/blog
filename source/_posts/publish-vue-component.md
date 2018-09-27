---
title: npm上发布vue组件
date: 2018-09-27 21:05:42
tags: vue
---

## 创建组件

- 通过 vue-cli 3 来创建一个组件

#### 为什么通过 vue-cli3

- 该脚手架允许构建不同的目标，包括应用，库

## 修改 src 目录文件如下

- src
  - index.js(插件入口)
  - fullpage.vue（组件）

## 修改 package.json 的 script

```javascript
"scripts": {
    "serve": "vue-cli-service serve",
    // build lib
    "build:lib": "vue-cli-service build --target lib --name v-fullpage ./src/index.js",
    "lint": "vue-cli-service lint"
}
```

## 打包生成第三方库

- yarn build:lib
- 根目录下就会出现 dist 文件夹，包含不同版本的 .js 文件，而 v-fullpage.umd.min.js 文件就是给到用户使用的第三方库了

## 上传 npm

- npm login
- package.json

```javascript
"name": "v-fullpage", // 包名,上传名字前到npm查看是否有同名
"description": "A Vue.js components",//对包的描述，在npmjs.com上搜索时会显示，有助于用户在搜索时进行筛选
"version": "0.2.0", // 每次发布需比上一个版本高,不然不给你发布的
"author": "youjie <419703726@qq.com>",
"private": false,
"main": "dist/v-fullpage.umd.min.js", // 入口,import pkg from 'package-name'时，其实导入的就是main定义的文件，它可以是CommonJs格式的, 也可以是umd格式。需要注意的是，当你把一个包发布到npm上时，它同时也可以在unpkg上获取到。也就是说，你的代码既可能在NodeJs环境也可能浏览器环境执行。为此你需要用umd格式打包，并且在package.json定义unpkg字段，一般而言此时它的命名为name.min.js
"repository": {
    "type": "git",
    "url": "https://github.com/yokiyokiyoki/vue-fullpage.git"
},
"bugs": {
    "url": "https://github.com/yokiyokiyoki/vue-fullpage/issues"
},
"keywords": [
    "vue",//有助于用户搜索
    "vue-component"
],
"private": false,//false才行
```

- npm publish
