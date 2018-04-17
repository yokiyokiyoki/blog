---
title: vue的一些问题集合（二）
date: 2018-04-17 14:19:46
tags: Vue
---

## '@/components/'的@

* webpack 可以配置 alias 配置别名，这个@一般是 src 目录的别名

## npm run dev 报端口已经在运行

* 可能你在运行其他项目，去修改一下默认端口

## 父组件怎么调用子组件的方法

* this.$refs 里面找到该实例,前提是先设置了 ref
* this.$children 里面找到该实例

## nginx 怎么配置

[一大波不靠谱的 nginx 配置](https://juejin.im/post/58bfc412da2f60124db5999a)
