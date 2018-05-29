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
