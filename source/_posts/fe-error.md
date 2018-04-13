---
title: 前端错误收集
date: 2018-02-12 11:27:51
tags:
---

## 为什么要进行前端的错误进行监控

* 现在网页的要求已经趋近于原生的应用，几乎都是有着大量交互的。面对各种用户，不同的浏览器等等出现的不同问题，有必要进行及时的监控，毕竟有些问题复现也是挺困难的。

## 捕获错误的方法

* try..catch 之前说过，但是它只能在 try 的块里运行才可以捕捉错误，无法捕捉全局的错误事件
* window.onerror 可以用来捕捉全局的错误,但是它无法捕捉异步错误，我们在 ajax 模块统一埋点

### window.onerror

```javascript
window.onerror=function(message,url,linNo,columnNo,error)
message:错误信息
url:发生错误对应的脚本路径，比如是bundle.js
lineNo:错误发生的行号
colunmnNo:错误发生的列号
error：具体的error对象，包含更加详细的错误调用堆栈信息
```

## 常见问题

### Script Error

> 现在我们一般都把资源放在 cdn 上，其他资源在本页面相当于跨域为什么会有这种设置，这是避免数据泄露到不安全的域中。如果我是银行页面，随便引入一个 js 资源，读取了账户密码，那可如何是好。

#### 解决手段

添加信任的域

* 客户端的 script 添加 crossorigin，他的作用就是告诉浏览器，要加载一个其他域的资源，并且信任他
* 服务端设置**Acess-Control-Allow-Origin**的响应头，可以直接设置为\*，信任全部资源。cdn 资源应该全部加上 CORS 响应头。
  > 如果我们使用 nginx 的话，可以像下面那样简单配置

```javascript
location / {
     root /Users/**;
     index  index.html index.htm;
     add_header "Access-Control-Allow-Origin" "*";
}
```

### 代码压缩

> 现代 web 工程都会直接压缩 js 代码，所以线上一般都是只有几行代码但是我们有 sourcemap，可以定位到源代码的位置。但是线上是没有这个东西，我们可以通过[sourcemap](https://github.com/mozilla/source-map/)这个工具来将压缩后的代码生成 sourcemap

* 这里简单介绍一下生成 sourcemap 文件的方式

```javascript
uglifyjs --source-map 最终生成的map名称 --output 压缩文件名称 原文件名称
```

* 简单通过 soucemap 定位到源文件的真正行数

```javascript
let sourceMap = require("source-map");
let mapData = require("./test.json");
let consumer = new sourceMap.SourceMapConsumer(mapData);
let info = consumer.originalPositionFor({ line: 1, column: 102 });
console.log(info); //{ source: "test.js", line: 11, column: 6, name: "yoki" }
```

## 推荐工具

> 由以上可以简单知道前端收集的原理了，但真正到线上肯定是不够的，这里有一些成熟的工具

* [sentry](https://sentry.io)
* [fundebug](https://www.fundebug.com/)

---

> 分享一篇干货[如何设计一个前端监控系统](http://web.jobbole.com/90033/)
