---
title: 使用set数据结构
date: 2018-04-22 14:19:12
tags: javascript
---

## 使用 set 数据结构

> set 是一个类似数组的数据结构，但是它没有重复值

### 如何生成 set 数据结构

* 直接 new Set()
* 往 new Set()传入具有 iterator 接口的数据结构，比如数组

```javascript
// 例一
const set = new Set([1, 2, 3, 4, 4]);
[...set];
// [1, 2, 3, 4]

// 例二
const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
items.size; // 5

// 例三
const set = new Set(document.querySelectorAll("div"));
set.size; // 56

// 类似于
const set = new Set();
document.querySelectorAll("div").forEach(div => set.add(div));
set.size; // 56
```

### set 实例属性和方法

#### 属性

* Set.prototype.constructor：构造函数，默认就是 Set 函数
* Set.prototype.size：返回实例的总数

#### 方法

##### 操作方法

* add(val)：添加某个值，返回 该实例
* delete(val):删除某个值，返回布尔值，表示是否成功
* has(val)：返回布尔值，表示是否拥有
* clear()：清空所有，返回值 void

##### 遍历方法

> set 结构是具有 itrator 接口的，可以直接 for..of 遍历，相当于下面的 values

* keys：返回键名的遍历器对象
* values：返回值的遍历器对象
* entries:返回键值对的遍历器对象
* forEach:使用回调函数遍历每个成员

> 由于 set 没有键名，只有键值，或者说键名和键值一样，所以 keys===values
