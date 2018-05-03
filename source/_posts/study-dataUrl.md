---
title: 浅谈Data URL
date: 2018-05-01 15:08:38
tags: javascript
---

## Data URL 是什么

是一种提供让外置资源直接内嵌在页面中的方案。格式如下：

### 格式

```javascript
data:[<mime type>][;charset=<charset>][;base64],<encoded data>
```

* 第一部分是`data：`协议头，它标识这个内容为一个 dataurl 资源
* 第二部分是 MIME 类型，表示这串内容的展现方式，比如 text/plain，就是文本展示;image/jpeg,就是以 jpeg 图片形式展示，同样浏览器会根据这个 MIME 类型来解析数据。
* 第三部分是编码设置，默认编码是 charset=US-ASCII，可以在浏览器输入框分别输入下面内容：

```javascript
// output: &auml;&frac12; &aring;&yen;&frac12; -> 使用默认的编码展示，故乱码
data:text/html,你好
// output: 你好 -> 使用 UTF-8 展示
data:text/html;charset=UTF-8,你好
// output: 浣犲ソ -> 使用 gbk 展示（浏览器默认编码 UTF-8，故乱码）
data:text/html;charset=gbk,你好
// output: 你好 -> UTF-8 编码，内容先使用 base64 解码，然后展示
data:text/html;charset=UTF-8;base64,5L2g5aW9
```

* 第四部分是 base64 编码设定，可选
* 最后一部分是这个 dataurl 承载的内容，可以是纯文本，也可以是经过 base64 编码的内容

平时我们经常见到的是这样的：

```javascript
//内嵌base64图片
background-image: url("data:image/gif;base64,R0lGODlhAwADAIAAAP///8zMzCH5BAAAAAAALAAAAAADAAMAAAIEBHIJBQA7");
```

## Data URL 的优劣

### 优点

* img 的 src 说明这个是外部资源，浏览器会向服务器拉取一次资源请求，占用网络资源
* 大多数浏览器都有一个并发请求数不能超过 4 个的限制。如果一个网页里嵌入了太多的外部资源，请求会使整个页面加载延迟。
* 图片体积太小的时候，不值得占用一个 http 会话

### 缺点

* base64 编码的数据体积是原数据的体积 4/3，也就是说 data url 形式的图片会比二进制格式的图片体积大 1/3
* data url 形式的图片不会被浏览器缓存，这意味着每次访问这样页面的时候都会被下载一次。如果这个图片被整个网站使用，就是一个使用效率问题（已被解决）

  > 如何将 data url 数据放入浏览器缓存，通过 css 样式文件。所有浏览器都会积极缓存 css 文件来提高页面加载效率。
