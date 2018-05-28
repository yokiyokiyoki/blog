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
