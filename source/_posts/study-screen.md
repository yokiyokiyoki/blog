---
title: 白屏和首屏时间
date: 2018-05-22 21:23:28
tags: 浏览器
---

# 白屏时间

> 浏览器开始显示内容的时间。从我们输入网址，到浏览器出现第一个字符的时间

* chrome 有自己的 api，window.chrome.loadTimes().firstPaintTime \* 1000 - window.performance.timing.navigationStart

## 如何计算

通常认为浏览器开始渲染 body 标签或者解析完 head 标签的时刻就是页面白屏结束的时间

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>白屏</title>
  <script type="text/javascript">
    // 不兼容performance.timing 的浏览器，如IE8
    window.pageStartTime = Date.now();
  </script>
  <!-- 页面 CSS 资源 -->
  <link rel="stylesheet" href="common.css">
  <link rel="stylesheet" href="page.css">
  <script type="text/javascript">
    // 白屏时间结束点
    window.firstPaint = Date.now();
  </script>
</head>
<body>
  <!-- 页面内容 -->
</body>
</html>
```

* 如果可以使用 performance api 的时候，白屏时间=firstPaint - performance.timing.navigationStart（浏览器打开网站开始）
* 如果不可以使用，比如 ie8，那么白屏时间= firstPaint - pageStartTime

# 首屏时间

> 指用户打开网站开始，到浏览器首屏内容渲染完成的时间。一般来说，5s 内优秀，10 之内可以接受，10s 以上不可以接受

## 怎么计算

> 1.首屏模块标签标记法，2.统计首屏内加载最慢的图片的时间，3.自定义首屏内容计算法

### 首屏模块标签标记法

* 适用于首屏内容不需要通过拉取数据才能生存以及页面不考虑图片等资源加载的情况。
* 但是现在大多数情况都需要通过接口拉数据才能完整显示，所以这个不太使用

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>首屏</title>
  <script type="text/javascript">
    window.pageStartTime = Date.now();
  </script>
  <link rel="stylesheet" href="common.css">
  <link rel="stylesheet" href="page.css">
</head>
<body>
  <!-- 首屏可见模块1 -->
  <div class="module-1"></div>
  <!-- 首屏可见模块2 -->
  <div class="module-2"></div>
  <script type="text/javascript">
    window.firstScreen = Date.now();
  </script>
  <!-- 首屏不可见模块3 -->
  <div class="module-3"></div>
    <!-- 首屏不可见模块4 -->
  <div class="module-4"></div>
</body>
</html>
```

### 统计首屏内图片完成加载的时间

* 通常首屏内容加载最慢的就是图片资源，因此我们会把首屏内加载最慢的图片的时间当作首屏时间
* 遍历首屏内所有图片标签，并且监听所有图片标签的 onload 事件，最终遍历图片标签的加载时间的最大值，然后用这个最大值减去 navigationStart

### 自定义模块内容计算法

* 由于统计图片加载时间比较复杂，通常会自定义模块内容，来简化
* 忽略图片等资源加载情况，只考虑页面主要 dom
* 只考虑首屏的主要模块，而不是严格意义首屏线上的所有内容
