---
title: 浏览器怎么渲染
date: 2018-04-10 10:26:50
tags: 浏览器
---

## 为什么要知道浏览器是怎么渲染的

* 作为一个前端工程师，了解浏览器的渲染过程，可以掌握优化的指导原则。
* 现在有那么多的优化方案，预编译，预加载，资源合并，按需加载等等都是针对浏览器渲染的优化。

### 关键渲染路径

* 关键渲染路径(Critical Rendering Path)是指与当前用户操作有关的内容
* 比如用户刚刚打开一个页面，首屏的显示就是当前用户操作的内容，具体就是浏览器收到 html/css/js 等资源并对其进行处理从而渲染出页面
* 了解浏览器渲染的过程与原理，很大程度上是为了优化关键渲染路径
* 为了保障首屏内容的最快速显示，通常会提到渐进式页面渲染，但是为了渐进式页面渲染，就要做资源的拆分，怎么拆分，这是按场景考虑的

## 浏览器渲染页面过程

* 1.dns 查询
* 2.tcp 连接
* 3.http 请求即响应
* 4.服务端响应
* 5.客户端渲染

### 客户端渲染

> 以下步骤不一定一次性顺序完成，如果 dom 或者 cssom 被修改，则以下过程需要重复执行，这样才能计算哪些像素在屏幕上需要重新渲染

* 1.处理 html 标记并构建 dom 树
* 2.处理 css 并构建 cssom 树
* 3.将 dom 树和 cssom 树合并成一个渲染树
* 4.根据渲染树来布局，以计算每个节点的几何信息
* 5.将各个节点绘制到屏幕上

## 阻塞渲染

> 现代浏览器是并行加载资源的。当 HTML 解析器(html parser)被脚本阻塞的时候，解析器虽然会停止构建 dom，但仍然会识别该脚本后面的资源，并进行预加载。

* 默认情况下，css 被视为阻塞渲染的资源，这意味着浏览器将不会渲染任何已处理的内容，直到 cssdom 构建完毕
* js 不仅可以读取和修改 dom 属性，还可以读取和修改 cssom 属性

> 存在阻塞的 css 资源的时候，浏览器就会延迟 js 的执行和 dom 构建

* 当浏览器遇到一个 script 标记的时候，dom 构建将暂停，直到脚本完成执行
* cssom 构建的时候，js 执行将暂停，直到 cssdom 就绪

> 所以 script 标签的位置很重要，实际使用的时候，可以遵循下面两个原则

* css 优先：引入顺序上，css 资源先于 js 资源
* js 应该尽量少影响 dom 的构建

### CSS

> 渲染树(Render-Tree)的关键渲染路径中，要求同时具有 dom 和 cssom，之后才会构建渲染树。所以 html 和 css 都是被阻塞的资源。html 一定是需要的，那么可以从 css 上想办法。

* 精简 css 并尽快提供它，例如将多个 css 合并成一个，并进行压缩

### JavaScript

* 实际工程，常常将 js 资源放到文档底部
* defer 和 async 可以改变阻塞模式

#### defer

* defer 属性表示延迟执行引入的 js，这段 js 加载时候 html 也并未停止解析，这两个过程是并行的
* 整个 document 解析完毕且 defer-script 也加载完成之后（这两件事情的顺序无关），会执行所有由 defer-script 加载的 JavaScript 代码，然后触发 DOMContentLoaded 事件。

```javascript
<script src="app1.js" defer></script>
<script src="app2.js" defer></script>
<script src="app3.js" defer></script>
```

* defer 不会改变 script 中代码的执行顺序，示例代码会按照 1，2，3 的顺序执行。

#### async

```javascript
<script src="app.js" async></script>
<script src="ad.js" async></script>
<script src="statistics.js" async></script>
```

* async 属性表示异步执行引入的 JavaScript，与 defer 的区别在于，如果已经加载好，就会开始执行——无论此刻是 HTML 解析阶段还是 DOMContentLoaded 触发之后。需要注意的是，这种方式加载的 JavaScript 依然会阻塞 load 事件。换句话说，async-script 可能在 DOMContentLoaded 触发之前或之后执行，但一定在 load 触发之前执行。
* 多个 async-script 的执行顺序是不确定的。所以 app.js 和 ad.js 和 statistics.js 不一定是顺序执行，谁先加载完谁先执行。
