---
title: 浅谈Data URL
date: 2018-05-01 15:08:38
tags: javascript
---

## Data URL 是什么

是一种提供让外置资源直接内嵌在页面中的方案。格式如下：

```javascript
data:[<mime type>][;charset=<charset>][;base64],<encoded data>
```

平时我们经常见到的是这样的：

```javascript
//内嵌base64图片
<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAUA
AAAFCAYAAACNbyblAAAAHElEQVQI12P4//8/w38GIAXDIBKE0DHxgljNBAAO
9TXL0Y4OHwAAAABJRU5ErkJggg==" alt="Red dot">
```
