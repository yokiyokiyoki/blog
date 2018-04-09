---
title: javascript装逼指南
date: 2017-12-03 18:53:56
tags: javascript
---

> 1.javascript 代码错误处理方式

```javascript
try {
  something;
} catch (e) {
  window.location.href = "http://stackoverflow.com/search?q=[js]+" + e.message;
}
```

> 2.如何优雅的取随机字符串

```javascript
Math.random()
  .toString(16)
  .substring(2);
Math.random()
  .toString(36)
  .substring(2);
```

> 3.如何优雅的取整

```javascript
let a = ~~2.33; //2

let b = 2.33 | 0; //2

let c = 2.33 >> 0; //2
```

> 4.金钱数字取千分位的非正则优雅实现

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
//toLocaleString
(23333333).toLocaleString("en-US");
("23,333,333");
```

> 5.最短代码实现数组去重

```javascript
[...new Set([1, "1", 2, 1, 1, 3])];
//[1,'1',2,3]
```

> 6.最短代码实现一个长度为 m(6)且值都为 n（8）的数组

```javascript
Array(6).fill(8);
//[8,8,8,8,8,8]
```

> 7.短路表达式

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

> 8.颜色 rgb 和 hex 的相互转换

```javascript
function rgbToHex(r, g, b) {
  return "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1);
}
function hexToRgb(hex) {
  var result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
  return result
    ? {
        r: parseInt(result[1], 16),
        g: parseInt(result[2], 16),
        b: parseInt(result[3], 16)
      }
    : null;
}

alert(hexToRgb("#0033ff").g); // "51";
```
