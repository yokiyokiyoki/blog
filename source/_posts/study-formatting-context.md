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
* display 为 inline-block, table-cell, table-caption(表格相关的)
* 在 BFC 中，每一个盒子的左外边缘（margin-left）会触碰到容器的左边缘(border-left)（对于从右到左的格式来说，则触碰到右边缘）
* overflow 不为 visible

# 推荐阅读

* [理解 BFC 的原理及其作用](https://blog.csdn.net/web_hwg/article/details/78470765)
* [深入理解 BFC 和 Margin Collapse](https://www.w3cplus.com/css/understanding-bfc-and-margin-collapse.html)
* [理解 css 中的 bfc](https://www.w3cplus.com/css/understanding-block-formatting-contexts-in-css.html)

# IFC

> 内联格式化上下文

## 如何触发

* 元素满足 inline-level box

## 布局特点

* 与 BFC 的区别是，水平方向的
* 内部元素全是 inline-level
* 如果由一个子节点是 block-level，父节点就要生成 BFC，但是 inline 元素怎么放进 BFC（浏览器会生成匿名盒子来容纳这些 inline 元素）
* IFC 中时不可能有块级元素的，当插入块级元素时（如 p 中插入 div）会产生两个匿名块与 div 分隔开，即产生两个 IFC，每个 IFC 对外表现为块级元素，与 div 垂直排列。
* 每一行的多个内联元素都会生成同一个 line-box（线框）
* 计算行框里的各行内级框的高度。对于置换元素、行内块元素、行内表格元素来说，这是边界框的高度，对于行内框来说，这是其 ‘line-height’。

## 具体作用

* 水平居中：当一个块要在环境中水平居中时，设置其为 inline-block 则会在外层产生 IFC，通过设置父容器 text-align:center 则可以使其水平居中。
* 垂直居中：创建一个 IFC，用其中一个元素撑开父元素的高度，然后设置其 vertical-align:middle，其他行内元素则可以在此父元素下垂直居中。

## 推荐阅读

* [浅析 CSS 中的 BFC 和 IFC](https://www.bbsmax.com/A/rV573pX5PD/)
* [line box，inline box 及 vertical-align 分析](https://segmentfault.com/a/1190000008723748)
