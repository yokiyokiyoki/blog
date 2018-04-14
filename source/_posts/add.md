---
title: add(1)(2)(3)解题思路
date: 2017-02-08 12:56:49
tags: javascript
---

## 投机解法

> 当时第一眼看到这个题目的时候，第一反应就是函数里返回一个新的函数

* 一个不灵活的解决方案,不具备拓展性

```javascript
function add(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}
add(1)(2)(3)=6
```

## 正确解法

* 函数最终的和要保存一个闭包当中

```javascript
function add(a) {
  //sum保存于闭包之中
  let sum = a;
  let tmp = function(b) {
    sum = +b;
    //返回自己方便日后调用
    return tmp;
  };
  //如果到这里什么都不写，那么console.log(add(1)(2)(3))是add这个函数的定义
  //console.log有些情况下会调用toString和valueOf方法，所以我们重写这个方法
  tmp.valueOf = tmp.toString = function() {
    return sum;
  };
  return tmp;
}
```
