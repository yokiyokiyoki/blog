---
title: 浅谈Iterator接口
date: 2018-04-21 21:47:18
tags: javascript
---

## 简单介绍 Iterator（遍历器）

> 为不同的数据结构提供统一的访问机制，只要数据结构有这种接口，就可以用 for..of 完成遍历

### 遍历过程

```javascript
let it = makeIterator(["a", "b"]);

it.next(); // { value: "a", done: false }
it.next(); // { value: "b", done: false }
it.next(); // { value: undefined, done: true }
```

* 第一次调用 next 方法返回数据结构的第一个成员，第二次就第二个直到没有

## 部署 Iterator 接口

> 当使用 for..of 遍历某种数据结构，就会自动去寻找 Iterator 接口

* es6 规定改接口默认部署在`Symbol.iterator`属性里面
* 返回一个遍历器对象，该对象的本质特征就是具有 next 方法
* 每次调用 next 方法，都会返回一个当前成员的信息对象

```javascript
//当执行for..of，会自动执行Symbol.iterator这个函数
const obj = {
  [Symbol.iterator]: function() {
    return {
      next: function() {
        return {
          value: 1,
          done: true
        };
      }
    };
  }
};
```

> 有些数据结构，本身就具有该接口

* Array
* Map
* Set
* String
* TypedArray
* 函数的 arguments 对象
* NodeList 对象
