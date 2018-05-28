---
title: 动画了解一下（二）
date: 2017-12-13 11:23:45
tags: css
---

## fps

* fps 是 frame per second，一秒能够重新渲染多少个画面
* 网页的每一帧都是一次重新渲染，一帧（frame）就是一个画面。
* 我们平时看电视，电视上的画面连续播放，其实一个画面接着一个画面的，如果一秒有 60 个画面播放，我们看着就很流畅了。如果一秒 10 个画面，可能就会感觉像在跳机械舞一样，一顿一顿的
* 现在大多数显示器的刷新频率都是 60Hz，浏览器一般也会自动按照这个频率，刷新动画。
* 所以网页动画能够做到每秒 60 帧，就可以和显示器同步刷新，视觉效果达到最佳。
* 一秒之内进行 60 次渲染，每秒不能超过 16.66 毫秒

> 如果想要到 60 帧的刷新率，那么 js 线程每个任务的耗时，必须少于 16ms。一个解决办法是用 web worker，主线程只用于 ui 渲染，其他和 ui 渲染不想干的任务，都放在 worker 线程。

## 调节渲染

> 有一些 js 方法可以调节重新渲染，大幅度提高网页性能

### requestAnimationFrame

* 他可以将代码放到下次重新渲染执行

```javascript
function doubleHeight(element) {
  var currentHeight = element.clientHeight;
  element.style.height = currentHeight * 2 + "px";
}
elements.forEach(doubleHeight);
//读了马上又去写，这就会马上重排，导致重新渲染，这就对网页性能很不利
//可以使用window.requestAnimation把读写操作分离，把所有写操作放到下一次浏览器正常的重新渲染
function doubleHeight(element) {
  var currentHeight = element.clientHeight;
  window.requestAnimationFrame(function() {
    element.style.height = currentHeight * 2 + "px";
  });
}
elements.forEach(doubleHeight);
```

* 适用于页面滚动事件，推迟到下一次重新渲染
* 适用于网页动画，比如

```javascript
//元素一帧旋转一度
var rAF = window.requestAnimationFrame;

var degrees = 0;
function update() {
  div.style.transform = "rotate(" + degrees + "deg)";
  console.log("updated to degrees " + degrees);
  degrees = degrees + 1;
  rAF(update);
}
rAF(update);
```

### requestIdleCallback

> 只有当一帧的末尾有空闲时间，才会执行回调

* requestIdleCallback(fn)
* 因为一秒 60 帧，一帧就 16.66ms，只有当前帧的运行时间小于 16.66ms 的时候，fn 才会执行。如果当前没有空闲时间，就推迟到下一帧，直到有空闲时间为止。
* requestIdleCallback(fn，5000)可以制定第二个参数，表示指定的 ms，表示这段时间内，如果每一帧都没有空间时间，fn 将强制执行

---

> [网页性能管理详解](http://www.ruanyifeng.com/blog/2015/09/web-page-performance-in-depth.html)
