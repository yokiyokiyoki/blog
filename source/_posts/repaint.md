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

## 怎样优化

### 批量读或者批量写

> 现代浏览器已经很智能，会把所有变动排在一起，然后一次性集中在一起，推入到一个队列然后再渲染，这样通过队列话修改然后批量执行可以优化重排过程。

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

### 合并样式操作

> 尽管现代浏览器做了优化，但是旧的浏览器可能没有，所以我们应该尽可能合并对样式的修改

* cssText：el.style.cssText='border-left: 1px; border-right: 2px; padding: 5px;'
* 修改 className：如果不是那么依赖计算，可以通过修改 className

### 修改 dom 元素的时候使其脱离文档流

> 当我们需要对 dom 元素频繁操作的时候，我们可以这样减少重排和重绘

* 让该 dom 元素脱离文档流（重排）
* 操作元素
* 完成后再添加到 document（重排）

#### 如何脱离文档流

* 隐藏元素：display:none，然后一系列操作后，再显示
* 推荐：使用 document fragment，就是传说中的文档碎片了，document.createDocumentFragment()，另外的 dom 子树，操作完后，再拷贝回来文档中，div.appendChild（fragment）
* 将原始元素拷贝到一个脱离文档的节点中，cloneNode,修改这个副本，然后再通过这个元素的父元素的 replaceChild 替换他

```javascript
let old = document.getElementById("mylist");
let clone = old.cloneNode(true);
appendDataToElement(clone, data);
old.parentNode.replaceChild(clone, old);
```
