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
