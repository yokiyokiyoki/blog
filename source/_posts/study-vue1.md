---
title: vue的一些坑（一）
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
