---
title: ajax使用FormData对象上传文件
date: 2018-05-11 20:24:14
tags: javascript
---

## 背景

* 因为发现用 postman 很轻松的就可以选择 file 类型
* 但是 ajax 直接 data 提交键值对却是报不合法的

```javascript
//报错的
this.$proxy({
  url: "/pgsls/kol/addExcel",
  type: "post",
  data: {
    brandId: "1",
    type: "1",
    file: file //文件类型
  }
});
```

## 解决方案

* 因为要上传类型是文件（二进制数据，而 http 提供的基于文本的通信协议），所以请求头的 Content-Type 要改成 multipart/form-data(默认是 application/x-www-form-urlencoded)

### HTTP 请求实体

> 每个字段由一段 boundary string 来分隔，浏览器保证该 boundary string 不与内容重复,因而 multipart/form-data 能够成功编码二进制数据

```javascript
------WebKitFormBoundaryWyjhf3VZMVeInmDe
Content-Disposition: form-data; name="brandId"

0
------WebKitFormBoundaryWyjhf3VZMVeInmDe
Content-Disposition: form-data; name="type"

1
------WebKitFormBoundaryWyjhf3VZMVeInmDe
Content-Disposition: form-data; name="file"; filename="test.xlsx"
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet


------WebKitFormBoundaryWyjhf3VZMVeInmDe--
```

### 实际发送

```javascript
let formData = new FormData();
formData.append("brandId", this.dialog.selectBrand);
formData.append("type", this.dialog.selectType);
formData.append("file", this.dialog.file);
return this.$proxy({
  url: "/pgsls/kol/addExcel",
  type: "post",
  data: formData,
  cache: false,
  processData: false,
  contentType: false
});
```

##### cacah:false

* cache 设为 false 可以禁止浏览器对该 URL（以及对应的 HTTP 方法）的缓存。 jQuery 通过为 URL 添加一个冗余参数来实现。
* 该方法只对 GET 和 HEAD 起作用，然而 IE8 会缓存之前的 GET 结果来响应 POST 请求。 这里设置 cache: false 是为了兼容 IE8。

##### contentType：false

* jq 的 contentType 默认值是 application/x-www-form-urlencoded,因此传给 data 参数的对象会默认转换为 query string
* 我们不需要 jq 做这个转换，否则会破坏掉 multipart/form-data 的编码格式。所以设置 false 来禁止 jq 的转换操作

##### processData:false

* jq 会将 data 对象转换为字符串来发送 http 请求，默认情况会用 application/x-www-form-urlencoded 编码转换
* 我们设置 contentType:false 后该转换会失败。因此设置 processData:false 来禁止这个转换过程（相当于双层保险）
* data 本来就是用 formData 编码好的数据，不需要进行字符串转换

## 兼容性

* 由于 formdata 对象是 XMLHttpRequest Level2 接口，需要 IE 10+, Firefox 4.0+, Chrome 7+, Safari 5+, Opera 12+
* 低版本浏览器只能用 form 表单了
