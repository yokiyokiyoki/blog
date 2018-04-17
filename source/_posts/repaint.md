---
title: 浅谈重排和重绘
date: 2018-04-10 19:37:30
tags: 浏览器
---

## 简单介绍

* 网页生成布局(重排 reflow)，页面绘制（重绘 repaint）一个个盒子元素
  > 重排一定会引起重绘，重绘不一定引起重排

- 为什么这样说，如果我改变了布局，比如改变边框宽度或者给段落增加文字，浏览器需要重新计算元素的几何属性，就会重排，然后就会重新绘制
- 但是如果改变元素的背景色，就只会发生重绘，而不会影响布局
- 重绘和重排非常消耗资源，应该尽量避免

## 批量读或者批量写

> 现代浏览器已经很智能，会把所有变动排在一起，然后一次性集中在一起，推入到一个队列然后再渲染

```javascript
div.stryle.color = "red";
div.style.marginTop = "30px";
//这里不会是一次次变，而是只会触发一次重排和重绘

//但是下面这样，就会触发两次重排和重绘
div.style.color = "blue";
var margin = parseInt(div.style.marginTop);
div.style.marginTop = margin + 10 + "px";
//第二行中，我们读取该div的元素位置，这个时候浏览器这个队列马上就要推出去渲染，才可以得知新的布局的位置，所以浏览器必须要重排
```

* 由上可知，如果我们在写操作之后，再读会立即渲染，有以下读操作

```javascript
offsetTop / offsetLeft / offsetWidth / offsetHeight;
scrollTop / scrollLeft / scrollWidth / scrollHeight;
clientTop / clientLeft / clientWidth / clientHeight;
getComputedStyle();
```

* 所以我们要懂得批量读和批量写

```javascript
// bad
div.style.left = div.offsetLeft + 10 + "px";
div.style.top = div.offsetTop + 10 + "px";

// good
var left = div.offsetLeft;
var top = div.offsetTop;
div.style.left = left + 10 + "px";
div.style.top = top + 10 + "px";
```
