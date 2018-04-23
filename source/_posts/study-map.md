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

> 需要注意的是，map 的 key 存的是内存地址，也就是说['a']和['a']不相等。基础值才相等

```javascript
const map = new Map();

map.set(["a"], 555);
map.get(["a"]); // undefined

const k1 = ["a"];
const k2 = ["a"];

map.set(k1, 111).set(k2, 222);

map.get(k1); // 111
map.get(k2); // 222
```

## 实例属性和方法

* size：返回成员总数
* set(key, value)：返回该实例，如果 key 有值会更新，没有就新生成
* get(key)：找到就返回，没有就 undefined
* has(key)：布尔值
* delete(key)：删除成功返回 true，失败 false
* clear：清除所有成员，没有返回值

> 遍历方法与 set 类似，但是没有 filter 和 map

* 比较有意思的是，map 结构默认的 iterator 接口就是 entries 方法

```javascript
for (let [key, value] of map.entries()) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"

// 等同于使用map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"
```

## 应用

### 与其他数据结构的互相转换

* map 转为数组，使用...运算符

```javascript
const myMap = new Map().set(true, 7).set({ foo: 3 }, ["abc"]);
[...myMap];
// [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
```

* 数组转为 map，观察数组结构

```javascript
new Map([[true, 7], [{ foo: 3 }, ["abc"]]]);
// Map {
//   true => 7,
//   Object {foo: 3} => ['abc']
// }
```

### 过滤和遍历，但是 map 本身没有 map 和 filter 方法,先转为数组，再塞回 map

```javascript
const map0 = new Map()
  .set(1, "a")
  .set(2, "b")
  .set(3, "c");

const map1 = new Map([...map0].filter(([k, v]) => k < 3));
// 产生 Map 结构 {1 => 'a', 2 => 'b'}

const map2 = new Map([...map0].map(([k, v]) => [k * 2, "_" + v]));
// 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}
```
