---
title: 学习一下Event Loop
date: 2018-07-04 16:57:08
tags: node
---

## 浏览器中 Event Loop

> 因为 js 最初是为了和浏览器交互，是一门非阻塞单线程语言。如果多线程处理 dom 可能会有一些意想不到的问题，比如一个线程中增加节点，另外一个线程删除节点。当然引入读写锁可以解决这个问题。

### 执行栈和队列

- js 在执行过程中会产生执行环境，这些执行环境会被顺序的加入到执行栈中。如果遇到异步的代码，会被挂起并加入到 task（有多种）队列中。一旦执行栈为空，event-loop 就会从 task 队列中拿出需要执行的代码并放入执行栈中执行。所以本质上来说 js 的异步还是同步行为。

### 任务源

- 不同的任务源会被分配到不同的 task 队列中，任务源可以分为微任务（microtask）和宏任务（macrotask）。
- 在 es6 中，microtask 被称为 jobs，macrotask 被称为 task

```javascript
console.log("script start");

setTimeout(function() {
  console.log("setTimeout");
}, 0);

new Promise(resolve => {
  console.log("Promise");
  resolve();
})
  .then(function() {
    console.log("promise1");
  })
  .then(function() {
    console.log("promise2");
  });

console.log("script end");
// script start => Promise => script end => promise1 => promise2 => setTimeout
```

- 微任务包括 process.nextTick,promise,Object.ovserve,MutationObserve
- 宏任务包括 script，setTimeout，setInterval,setImmediate,I/O,UI rendering
- 很多人认为微任务一定快于宏任务，其实是错的。因为宏任务中包括了 script，浏览器会执行一个宏任务，接下来有异步代码的话会先执行微任务。

### 正确的一次 Event-loop 顺序

- 执行同步代码，这属于宏任务
- 执行栈为空，查询是否有微任务需要执行
- 执行所有微任务
- 必要的话渲染 ui
- 开始下一轮 event-loop，执行宏任务中的异步代码

所以宏任务中的异步代码有大量的计算并且需要操作 dom 的话，为了更快的界面响应，我们可以把操作 dom 放入微任务中。思考一下 vue ～

## node 中的 event loop

> node 中的 event loop 和浏览器的不一样，分为以下六个阶段，他们会按照顺序反复执行

### timers

- 会执行 setTimeout 和 setInterval
- 一个 timer 指定的事件并不是准确时间，而是在达到这个时间后尽快执行回调，可能会因为系统正在执行别的事务而延迟。
- 下限的时间会有个范围

### I/O callbacks

- 会执行除了 close 事件，定时器和 setImmediat 的回调

### idle，prepare

- idle，prepare 阶段内部实现

### poll 阶段

- poll 阶段很重要，这一阶段中，系统会做两件事情
  - 执行到点的定时器
  - 执行 poll 队列中的事件
- 如果有别的定时器需要被执行，会回到 timer 阶段执行回调

### check 阶段

- 执行 setImmediate

### close callbacks

- 执行 close 事件

## node 中执行顺序举例

- 有些情况下，定时器执行顺序是随机的

```javascript
setTimeout(() => {
  console.log("setTimeout");
}, 0);
setImmediate(() => {
  console.log("setImmediate");
});
// 这里可能会输出 setTimeout，setImmediate
// 可能也会相反的输出，这取决于性能
// 因为可能进入 event loop 用了不到 1 毫秒，这时候会执行 setImmediate
// 否则会执行 setTimeout
```

- 有些情况下是一定的

```javascirpt
var fs = require('fs')

fs.readFile(__filename, () => {
    setTimeout(() => {
        console.log('timeout');
    }, 0);
    setImmediate(() => {
        console.log('immediate');
    });
});
// 因为 readFile 的回调在 poll 中执行
// 发现有 setImmediate ，所以会立即跳到 check 阶段执行回调
// 再去 timer 阶段执行 setTimeout
// 所以以上输出一定是 setImmediate，setTimeout
```

- 上面都是 macrotask 的执行情况，microtask 会在以上每个阶段完成后立即执行

```javascript
setTimeout(() => {
  console.log("timer1");

  Promise.resolve().then(function() {
    console.log("promise1");
  });
}, 0);

setTimeout(() => {
  console.log("timer2");

  Promise.resolve().then(function() {
    console.log("promise2");
  });
}, 0);

// 以上代码在浏览器和 node 中打印情况是不同的
// 浏览器中打印 timer1, promise1, timer2, promise2
// node 中打印 timer1, timer2, promise1, promise2
```

- node 中的 process.nextTick 会优先于其他微任务执行
