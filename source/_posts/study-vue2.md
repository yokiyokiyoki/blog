---
title: vue的一些问题集合（二）
date: 2018-04-05 14:19:46
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

## v-if 和 v-show

> v-if 是 dom 没有插入到文档里面，等条件成立，才 append 到 document 里面。

* 等拿到值才可以处理组件内部的逻辑的，不然会报错
* 有些 v-for 但是还没有拿到值的

> v-show 只是简单通过 css 隐藏了

* 不会导致页面重绘，改善用户体验

## axios 的请求后台不接受

* axios 默认是 json 格式提交，如果后台只支持表单序列化，就要自己转义

## css 的 scoped 有什么功能

* 给每个类或者 id 自动添加 hash，只能给该组件用，不同组件不能进行继承

```javascript
/ 写的时候是这个
.a{}

// 编译过后,加上了 hash
.a[data-v-1ec35ffc]{}
```
