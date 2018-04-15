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

### 数组

```javascript
//全是数字的数组
const numArr: number[] = [1, 2, 3];
//数字和字符串都有的数组
const tmp: (number | string)[] = [1, "2", 3];
//一个可能什么都有类型都有的数组
const anyArr: any[] = [1, "1", true];
```

### 函数

```javascript
//传入参数是数字，返回的也是数字
function sum(x: number, y: number): number {
  return x + y;
}
//可选型，用?,sum2(1)可以，sum（1，2）也可以
function sum2(x:number,y?:number):number{
    return y?:x+y:x
}
//参数默认值,sum3(1,'5')，y会被自动推导为number
function sum3(x?:number,y=0){
    return x+y
}
//剩余参数
function concat(arr:any[],...items:any[]){
    return arr.concat(items)
}
concat([],1,2,3)=[1,2,3]
```

### 接口

* 在面向对象，接口是一个很重要的概念，他是对行为的抽象，而具体如何行动需要由 class 去实现

* ts 的接口是非常灵活的，既可以对类的一部分行为进行抽象，也可以用于对对象的形状进行描述

```javascript
//对对象形状的描述
interface Person{
    name:string
    age?:number,
    say?:()=>void
}
const yoki:Person={
    name:'yoki',
    age:18
}
//只读属性，不可修改
interface Person{
    readonly id:number
}
```

### 类

> * public 修饰的属性或者方法都是公有的，可以在任何地方被访问到，默认所有的属性或方法都是 public

> * private 修饰的属性或者方法都是私有的，不能在声明它的类的外部访问
> * protected 修饰的属性或者方法都是受保护的，它与 pravate 类似，区别是它在子类是允许访问的

```javascript
class Animal{
    publick name;
    public constructor(name){
        this.name=name
    }
}
let a=new Animal('rabbit')
a.name//rabbit
a.name='tom'//tom
class Animal2{
    private name;
    public constructor(name){
        this.name=name
    }
}
let b=new Animal('rabbit')
b.name='tom'//报错
class Animal3{
    protected name;
    public constructor(name){
        this.name=name
    }
}
class Cat extends Animal{
    constructor(name){
        super(name)
        console.log(this.name)//可以的
    }
}
```
