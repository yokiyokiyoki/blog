---
title: 浅谈同构（CSR+SSR）
date: 2018-05-24 10:45:28
tags: 浏览器
---

# 什么是同构

> 维基百科：在抽象代数中，同构（英语：isomorphism）指的是一个保持结构的双射。在更一般的范畴论语言中，同构指的是一个态射，且存在另一个态射，使得两者的复合是一个恒等态射。

* 一套代码既可以在服务端运行，又可以在客户端运行，这就是同构应用。
* 服务端直出（html 字符串）和客户端渲染的组合，能够充分结合两者的优势，并有效避免两者的不足。
* 具备单页应用和多页应用的所有优势

## 服务端渲染和客户端渲染

> 以下叫做 SSR 和 CSR

* ssr 的话，服务器返回的结构相对完整的 html 文件，通过解析 html 文件，浏览器就可以渲染出来页面。
* csr 的话，拿到的只是包含 js 代码的 html 文件，在浏览器渲染页面之前，需要动态创建 html 标签

### CSR 的优势

> 即单页应用的优势

* 局部刷新。不用每次都进行完整页面请求。
* 懒加载。如在页面初始的时候可以只加载可视区域内的数据，滚动后加载别的。
* 富交互。js 实现各种炫酷效果。
* 节约服务器成本。省电省钱。

### SSR 的优势

* 不需要下载一堆 js 和 css 后才能看到首页（首屏性能）
* SEO
* 对于电量不给力的手机，减少在客户端的电量消耗

### 推荐阅读

* [“服务端渲染”吊打“客户端渲染”的那些事](https://w3ctech.com/topic/2005)

# 为什么要同构

* 通过 Node 直出，将传统的三次 http 请求简化成一次 http 请求，降低首屏渲染时间
* seo：服务端渲染对搜索引擎的爬取有着天然的优势

# 同构的实现策略

> 要实现同构，首先要正视一点，全盘同构是没有意义的。服务器端和浏览器端是两个不同的平台和环境，他们专注于解决各自的问题。

* 能够同构的代码，直接复用
* 无法同构的代码，封装成形式同构（具体看[react 同构与性能极致优化](http://web.jobbole.com/93421/)）
  * 比如 User-Agent 字符串，浏览器端直接 navigation.userAgent，服务器端需要 req.get('user-agent')

# 同构并没有想象中那么美

> 框架可以尽力帮我们做好，比如 next.js

* 性能：把原来放在几百万浏览器端的工作拿过来给服务器端做，计算力大。个性化的缓存，可以把每个用户个性化信息缓存到浏览器，这是一个天生的分布式缓存系统。
* 不容忽视的服务器端和浏览器环境差异：dom api 在客户端找不到等
* 内存溢出：前端代码由于浏览器环境刷新一遍内存重置的天然优势，对内存溢出没有充分考虑。
* 异步操作：前端可以做非常复杂的请求合并和延迟处理

# 推荐阅读

* [精读前后端渲染之争](https://github.com/camsong/blog/issues/8)
* [同构 mvc 的前端实践](https://github.com/Lucifier129/Lucifier129.github.io/issues/14)
* [react 同构与性能极致优化](http://web.jobbole.com/93421/)
* [解密 Vue SSR](https://juejin.im/post/5b063962f265da0ddb63dac3?utm_medium=fe&utm_source=weixinqun)
