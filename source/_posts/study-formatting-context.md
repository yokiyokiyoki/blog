---
title: css中的四种fc（格式上下文）
date: 2018-05-28 11:47:47
tags: css
---

# 什么是 FC

> formatting context 的缩写，格式上下文

* 是 css2.1 的一个规范
* 是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用
* css2.1 定义了两个格式上下文
  * BFC（block）
  * IFC（inline）
* css3 定义了两个格式上下文
  * GFC(grid)
  * FFC(flex)

# 概念

* block-level boxes:display 属性值为'block'，‘list-item’,‘table’会生成一个 block-level box，这样盒子会参与到 bfc（一种布局方式）中
* containing block:在浏览器内，想要把一个元素画出来，至少要知道定位和尺寸。定位有三种，无论哪一种都要先找到所在的 containing block，相当于一个大箱子里面摆很多小盒子，小盒子怎么摆取决于大箱子

# BFC

> 是一个独立的渲染区域，只有 block-level boxes 参与，规定了内部的 block-level box 如何布局，并且与这个区域外部毫不相干。

## 布局规则

* 内部的 box 会在垂直方向一个接着一个的放置
* box 垂直方向的距离由 margin 决定，属于同一个 BFC 的两个相邻 box 的 margin 会发生重叠
* bfc 的区域不会与 float box 重叠（可以用来实现两栏布局）
* bfc 就是页面上一个隔离的独立容器，容器里面的子元素不会影响到外面的元素

## 如何触发

* 根元素（body）
* float 属性不为 none
* position 为 abs 或者 fixed
* display 为 inline-block, flex, inline-flex,table-cell, table-caption(表格相关的)
* 在 BFC 中，每一个盒子的左外边缘（margin-left）会触碰到容器的左边缘(border-left)（对于从右到左的格式来说，则触碰到右边缘）著作权归作者所有。
* overflow 不为 visible

# 推荐阅读

* [理解 BFC 的原理及其作用](https://blog.csdn.net/web_hwg/article/details/78470765)
* [深入理解 BFC 和 Margin Collapse](https://www.w3cplus.com/css/understanding-bfc-and-margin-collapse.html)
