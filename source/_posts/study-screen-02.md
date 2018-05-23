---
title: 前端性能之用户可操作时间
date: 2018-05-23 11:53:12
tags: 浏览器
---

# 官方的一个浏览器从打开一个页面内部的一系列状态

* 用 performance.timing 的 api 就可以获取到各个阶段的
* 一般 start 和 end 间包括一个阶段
* 具体可以观察推荐阅读的那张图
* navigationStart 到 responseEnd 之间是网络传输层面
* domLoading 到 domComplete 是服务器传输回字节后浏览器的各种事件状态
  * domLoading
  * domInteractive(dom tree 构建完成)
  * domContentLoadedEventStart(触发的是 domContentLoaded 事件，这个很重要，network 都有体现)
  * domContentLoadedEventEnd
  * domComplete（资源全部加载完成，包括异步 js）
  * loadEventStart（触发 load 事件）
  * loadEventEnd

# 用户可操作时间

> 用户可以进行正常的事件输入交互操作

* 就是 domContentLoadedEventEnd-navigationStart 的时间
* jq 的$()就是指 domContentLoaded 事件

# 一些知识点

* document 到达 domInteractiv 状态的时候，代表 dom 树的构建完成
* 浏览器拿到文档首字节之后，也就是 responseEnd 之后。浏览器将 html 解析并构建成 DOM tree，同时将 css 解析成 cssom，这个过程是同步的
* 如果有 js 参与，因为同步的 js 可以改写文档在任何节点，所以 domtree 一旦碰上同步的 script 标签就会停止构建
* js 能查询 dom 对象的可被计算的样式，cssom 构建完之后才轮到 js 执行，但是这是指 css 文件在 js 之前，如果 css 在 js 之后，不会阻塞 js

# 推荐阅读

* [前端性能的几个基础指标](https://segmentfault.com/a/1190000005784687)
