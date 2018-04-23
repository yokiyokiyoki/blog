---
title: 使用map数据结构
date: 2018-04-21 15:10:22
tags: javascript
---

## object 和 map

> object 映照现实生活的物体，但是想使用 key-value 的遍历结构最好使用 map 数据结构

* object 结构实际上是字符串—值的结构
* map 具有 iterator 接口，并且是值-值对映射，也就是 key 可以是 function，obj 等

```javascript
const data = {};
const element = document.getElementById("myDiv");
//由于对象只接受字符串为键名，所以element被自动转化字符串
data[element] = "metadata";
data["[object HTMLDivElement]"]; // "metadata"
```

## 生成 map 结构

* new Map()
* 往 new Map()里面传入任何具有 iterator 接口的数据结构，包括数组等，甚至包括 map 本身
