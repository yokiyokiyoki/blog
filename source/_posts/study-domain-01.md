---
title: 浅谈前端跨域（一）
date: 2018-05-16 20:14:18
tags: 浏览器
---

## 什么是跨域

- 因为有浏览器同源策略限制，当前域名的 js 只能读取同域下的窗口属性
- 跨域不仅仅是指跨域名。
- 只要协议，域名，端口有任意不同，都被当作不同的域。

## 解决跨域问题

### 主域相同的跨域

- 如果主域相同，比如 a.com 和 b.a.com，那么可以在 b.a.com 设置

```javascript
document.domain = "a.com";
```

### 完全不同源的跨域（两个页面之间的通信）

#### window.name 跨域（同窗口）

- window 对象有个属性 name，在一个窗口的生命周期内，窗口载入的所有页面都是共享一个 window.name，每个页面对于 window.name 都有读写的权限
- window.name 是持久存在一个窗口载入过的所有页面中的。
- window.name 的神奇之处在于 name 值在不同的页面（甚至不同域名）加载后依旧存在（如果没修改当然就不会变化）
- 可以支持非常长的 name 值（2mb）

#### window.postMessage（跨窗口）

- html 为了解决这个问题，引入了一个全新的 api，跨文档通信 API，它为 window 对象新增了 postMessage 的方法
- 允许跨窗口通信，不论这个两个窗口是否同源

```javascript
//父窗口http://aaa.com向子窗口http://bbb.com发消息，调用postMessage方法。
let popup = window.open("http://bbb.com", "title");
popup.postMessage("Hello World!", "http://bbb.com");
```

- postMessage 的第一个参数是具体的信息内容，第二个参数是接受消息的窗口的源（origin），也可以设置为‘\*’，表示向所有窗口发送
- 父窗口和子窗口都可以通过 message 事件，监听对方的消息。message 事件的事件对象 event，有三个属性
  - event.source:发送消息的窗口
  - event.origin:消息发向的网址
  - event.data:消息内容

```javascript
var onmessage = function(event) {
  var data = event.data; //消息
  var origin = event.origin; //消息来源地址
  var source = event.source; //源Window对象
  if (origin == "http://www.aaa.com") {
    console.log(data); //hello world!
  }
  source.postMessage("Nice to see you!", "*");
};
if (typeof window.addEventListener != "undefined") {
  window.addEventListener("message", onmessage, false);
} else if (typeof window.attachEvent != "undefined") {
  //ie
  window.attachEvent("onmessage", onmessage);
}
```
