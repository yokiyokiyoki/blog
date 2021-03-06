---
title: Http表单的常见编码
date: 2018-05-11 23:14:51
tags: Http
---

## 什么是 http 表单

> web 最常见的就是 post 和 get 方法。其中 get 一般将参数编码在 url 中来传递数据，post 或者 put 必须要将数据放在 http 请求实体中，这样的数据就是 http 表单

* 表单数据的编码方式是 http 头部设置 Content-Type
* Content-Type 可以是任意 MIME 类型
* 下面五种最为常见
  * URLencoded：application/x-www-form-urlencoded
  * mutipart:mutipart/form-data
  * json:application/json
  * xml:text/xml
  * 纯文本：text/plain
* html 的 form 表单支持 urlencoded，mutipart 和 plain text 的编码，通过 enctype 属性来设定。
* ajax 默认是 json 格式的编码

### URLencoded

> form 表单的 enctype 默认就是 urlencoded

```javascript
<form  method='post' enctype='application/x-www-form-urlencoded'>
  <input type="text" name='title'>
  <input type="text" name='subtitle'>
  <input type="submit">
</form>
//http请求头
Content-Type: application/x-www-form-urlencoded
//请求体
title=test&subtitle=%E4%B8%AD%E5%9B%BD=>base64编码（url通用的编码，可以在chrome控制台用decodeURI解码）
```

### JSON

```javascript
$.post('/xxx', {
    title: 'test',
    content: [1,2,3]
});
Content-Type: application/json;charset=utf-8
//请求实体
{"title":"test","content":[1,2,3]}
```

### XML

```javascript
Content-Type: text/xml
//请求实体
<!--?xml version="1.0"?-->
<methodcall>
    <methodname>examples.getStateName</methodname>
    <params>
        <param>
            <value><i4>41</i4></value>
    </params>
</methodcall>
```
