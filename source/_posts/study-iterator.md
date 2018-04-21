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
