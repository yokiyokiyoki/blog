---
title: javascript装逼指南
date: 2018-12-03 18:53:56
tags: javascript
---

> 1.如何用代码写出 sb

```javascript
(!(~+[])+{})[--[~+""][+[]]*[~+[]] + ~~!+[]]+({}+[])[[~!+[]]*~+[]]
输出 'sb'
```

> 2.如何用代码写出 nb

```javascript
([][[]]+[])[+!![]]+([]+{})[!+[]+!![]]
输出 'nb'
```

> 3.javascript 代码错误处理方式

```javascript
try {
  something;
} catch (e) {
  window.location.href = "http://stackoverflow.com/search?q=[js]+" + e.message;
}
```

> 4.如何优雅的取随机字符串

```javascript
Math.random()
  .toString(16)
  .substring(2);
Math.random()
  .toString(36)
  .substring(2);
```

> 5.匿名 IIFE 的 n 种写法

```javascript
(function() {})();
(function() {})();
[(function() {})()];

~(function() {})();
!(function() {})();
+(function() {})();
-(function() {})();

delete (function() {})();
typeof (function() {})();
void (function() {})();
new function() {}();
new function() {}();

var f = (function() {})();

1, (function() {})();
1 ^ (function() {})();
1 > (function() {})();
```

> 6.如何优雅的取整

```javascript
let a = ~~2.33; //2

let b = 2.33 | 0; //2

let c = 2.33 >> 0; //2
```

> 7.金钱数字取千分位的非正则优雅实现

```javascript
//用reduce
function formatCash(str) {
  return str
    .split("")
    .reverse()
    .reduce((prev, next, index) => {
      return (index % 3 ? next : next + ",") + prev;
    });
}
console.log(formatCash("1234567890")); // 1,234,567,890
```

> 8.最短代码实现数组去重

```javascript
[...new Set([1, "1", 2, 1, 1, 3])];
//[1,'1',2,3]
```

> 9.最短代码实现一个长度为 m(6)且值都为 n（8）的数组

```javascript
Array(6).fill(8);
//[8,8,8,8,8,8]
```

> 10.短路表达式

```javascript
var a = b && 1;
// 相当于
if (b) {
  a = 1;
} else {
  a = b;
}

var a = b || 1;
// 相当于
if (b) {
  a = b;
} else {
  a = 1;
}
```
