---
title: vue的一些坑（一）
date: 2018-04-17 11:25:32
tags: Vue
---

> 在公司一直用 vue 开发，有时候被一些小伙伴经常问到的问题，这里收集一下勉强再挖个坑

## npm install 安装超时

* cnpm:npm i cnpm -g
* yarn 和 npm 改成淘宝源

```javascript
npm config : npm config set registry https://registry.npm.taobao.org
yarn config : yarn config set registry https://registry.npm.taobao.org
```
