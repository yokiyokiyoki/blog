---
title: vue的一些问题集合（一）
date: 2018-04-02 11:25:32
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

## data functions should return an object

这是因为你肯定在书写单组件，而单组件的 data 必须返回一个单例，可以参见[data 必须是函数](https://cn.vuejs.org/v2/guide/components.html#data-%E5%BF%85%E9%A1%BB%E6%98%AF%E5%87%BD%E6%95%B0)

* 这里简单解释下，如果一个组件 data 引用的是一个同一个对象指针，那么当你在另外一个组件应用这个组件，v-for 他的时候，比如三个，那么你修改其中一个组件，另外两个这个组件的状态也会改变，但是我们的原则是组件间相互隔离，互不影响。

## 在函数内写了 this.name（data 里存在 name），却报错 Cannot set property 'xxx' of undefined

> 你期待 this 指向 vue 实例，实际上它指的是 window，this 和当前上下文有关

* promise 的回调
* setInterval

比较好的解决方案就是使用箭头函数，绑定当前对象为 this 的上下文

## Component template shold contain exactly one root element.If you are useing v-if on multiple elements

* 单组件文件里必须要有个根元素，一个大 div

## No 'Access-Control-Allow-Origin' header is present on the requested resource.

> 跨域问题

* CORS 前后端配置
* nginx 反向代理，一般线上使用
* 本地开发，脚手架 vue-cli 里有 proxyTable

```javascript
// target : 就是 api 的代理的实际路径
// changeOrigin: 必须是变源
// pathRewrite : 路径重定向
proxyTable: {
  "/api": {
    target: "http://yoki.com",
    changeOrigin: true,
    // pathRewrite: {
    //   "^/api": "/"
    // }
  }
}
```

## 数组里的值手动更新了，为什么视图不更新

> this.arr[0]=1,但是视图没更新

* 这是因为 vue 重写了数组的原生方法，使用了数组方法 push，pop 等，会对其观察，并响应到视图。究其原因是 js 无法检测到到数组的变化
* 可以使用 this.$set
* 可以数组的方法更改

## 为什么根目录下会有 lock 文件

> 先简单介绍一下 A.B.C：A 是 major 版本表示和上个版本不兼容的大更改，B 是 minor 版本增加了新功能，可以向后兼容，C 是 patch 版本修复了 bug 向后兼容

* npm 上包那么多，说不定什么时候就会更新，根据 package.json 里的^(会更新到 major 版本号最新的),~（会更新到 minor 版本最新的）
* 有些包甚至不遵循发包规范
* 所以我们需要统一版本号，这样不同的电脑在不同的时间就会下载到一样的包了

## npm i -S 和-D 的区别

* -S 会放到 dependencies 里面，存放到线上能访问到的代码，比如 vue，vue-router
* -D 会放到 devDependcies：处于开发模式所依赖的模块，不产生额外代码到生产环境，比如 babel-core

## 单页应用没办法做 seo 吗

* spa 的页面由 javascript 根据 API 发回的数据在用户的浏览器里自动生成的，Baidu 不会看 Javascript，那么页面对于它来说就是一个空洞洞的 html 和很多 scirpt 元素
* 解决方案：服务端渲染(SSR)可以,请求回来是一个处理好的 html，vue 的服务端渲染方案[nuxt](https://zh.nuxtjs.org/)

## 为什么首屏加载这么慢

> 打包文件文件很大

* 减少 jq 这种库的引入，现在都是数据驱动
* webpack 压缩 js，css
* 路由懒加载，代码分割
* 采用服务端渲染，可以避免浏览器去解析模板和指令
