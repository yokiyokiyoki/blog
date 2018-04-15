---
title: 简单介绍Typescript
date: 2018-03-30 15:28:20
tags: Typescript
---

## 简单介绍

> 以下来自维基百科

* TypeScript 是一种由微软开发的自由和开源的编程语言。它是 JavaScript 的一个严格超集，并添加了可选的静态类型和基于类的面向对象编程。C#的首席架构师以及 Delphi 和 Turbo Pascal 的创始人安德斯·海尔斯伯格参与了 TypeScript 的开发。

* TypeScript 设计目标是开发大型应用，然后转译成 JavaScript。[7]由于 TypeScript 是 JavaScript 的严格超集，任何现有的 JavaScript 程序都是合法的 TypeScript 程序。

## 为什么需要 ts

> 普通的 js：假设我一个函数里面只能对字符串进行操作，要是我传了数字，直接就报错了，由此可见

* 增加了代码的可读性和可维护性
* 在编译阶段就能发现错误
* 类型推论

同时与 JavaScript 兼容良好，把.js 改为.ts 就能用

## 基本使用

### 基础类型

```javascript
//布尔值
const isDone = (boolean = false);
// 数值
const num: number = 6;
//字符串
const str: string = "yoki";
//空值返回
function say(): void {
  console.log("hi");
}
//null，undefined
const u: undefined = undefined;
const n: null = null;
```
