---
title: 浅谈浏览器跨域（二）之最终方案
date: 2018-05-16 21:29:55
tags: 浏览器
---

# 单向跨域（一般用来获取数据）

> 上面是两个页面之间的交互，现在是单方面向外面聊天的

## JSONP

> JSON With Padding

* 原理是 html 的 script 标签可以加载并执行其他域的 js 文件。
* 一般 query 是 callback，当然是其他也是，只不过现在和服务端约定好了，服务队会获取到 req.query.callback 这样

```javascript
<script type="text/javascript">
    function dosomething(jsondata){
        //处理获得的json数据
    }
</script>
<script src="http://example.com/data.php?callback=dosomething"></script>

//服务器
<?php
$callback = $_GET['callback'];//得到回调函数名
$data = array('a','b','c');//要返回的数据
echo $callback.'('.json_encode($data).')';//输出
?>
//服务器会返回domething(['a','b','c'])给浏览器，就可以直接执行了
```

* 优点是兼容性好，并且在请求完毕后可以通过调用 callback 的方法回传结果
* 缺点是支持 get 请求，传输的数据长度有限制
* 缺点是错误处理机制不完善，没办法进行错误处理
* 更主要的缺点是安全问题，如果被不信任的服务使用的时候会很危险，比如，callback 参数恶意添加标签（如 script，造成 xss 漏洞）

## websocket 协议

* 是一种通信协议，使用 ws://和 wss（加密）://
* 这个协议不实行同源策略，只要服务器支持，就可以

## 通过 CORS 跨域

* Cross-Origin Resource Sharing 跨域资源共享
* 规定了浏览器应该如何与服务器如何通过，背后的思想是使用自定义的 http 头部让浏览器和服务器沟通，从而决定请求或者响应是成功还是失败
* 浏览器不能低于 IE10
* 对于开发者来说 CORS 通信和同源的 ajax 通信没有差别，代码完全一样。浏览器一旦发现 ajax 请求跨域，就会自动添加一些附加的头部信息，有时还会多处一次附加的请求，但是用户不会有感觉
  > 实现 CORS 通信的关键是服务器，只要服务器实现了 CORS 接口，就可以跨源通信

```javascript
//相对路径是本源
<script type="text/javascript">
  var xhr = new XMLHttpRequest(); xhr.open("POST", "/damonare",true);
  xhr.send();
</script>
//绝对路径，跨域，无感
<script type="text/javascript">
    var xhr = new XMLHttpRequest();
    xhr.open("￼GET", "http://segmentfault.com/u/trigkit4/",true);
    xhr.send();
</script>
```

### 两种请求

> cors 分为两类，简单请求和非简单请求

##### 简单请求

满足下面三个条件，就属于简单请求

* 请求方法是 get，post，head
* (Content-Type)只能是 application/x-www-form-urlencoded, multipart/form-data 或 text/plain 中的一种
* 不会使用自定义请求头（类似于 X-Modified 这种）

###### 基本流程简单

* 请求的话，浏览器直接发出 CORS 请求。具体就是浏览器发现这次请求是简单请求，就主动在头信息之中，增加一个 origin 字段

```javascript
//让我们看看，在这个场景中，浏览器会发送什么的请求到服务器，而服务器又会返回什么给浏览器：
GET /resources/public-data/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130
Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Referer: http://foo.example/examples/access-control/simpleXSInvocation.html
Origin: http://foo.example //该请求来自于 http://foo.exmaple。
//以上是浏览器发送请求

HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 00:23:53 GMT
Server: Apache/2.0.61
Access-Control-Allow-Origin: * //这表明服务器接受来自任何站点的跨站请求。如果设置为http://foo.example。其它站点就不能跨站访问 http://bar.other 的资源了。
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked
Content-Type: application/xml
//以上是服务器返回信息给浏览器
```

###### 服务器返回的与 CORS 有关的字段

> 一般都是以 Access-Control 开头

* Access-Control-Allow-Origin 接受域名的请求，必须的
* Access-Control-Allow-Credentials 可选，表示允不允许发送 cookie。如果需要发送，浏览器的 ajax 请求要打开 withCredentials 属性（xhr.withCredentials = true;）
* Access-Control-Expose-Headers 可选。因为 cors 请求，xhr 对象的 getResponseHeader()方法只能拿到 6 个基本字段：Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma。如果想要拿到其他字段，只能在服务器里面指定，比如 getResponseHeader('FooBar')可以返回 FooBar 字段

##### 非简单请求

> 非简单请求是对服务器有特殊要求的请求，比如请求方法是 put 和 delete，或者 content-type 是 application/json

* 会在正式通信之前，增加一次 http 查询请求，称为预检请求
* 浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些 HTTP 动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的 XMLHttpRequest 请求，否则就报错
* 预检请求的方法是 options，表示这个请求是询问的
* 浏览器发出的预请求

```javascript
//浏览器
var url = "http://api.alice.com/cors";
var xhr = new XMLHttpRequest();
xhr.open("PUT", url, true);
xhr.setRequestHeader("X-Custom-Header", "value");
xhr.send();
//http头
OPTIONS /cors HTTP/1.1
Origin: http://api.bob.com
Access-Control-Request-Method: PUT//（必须的）告知请求是什么方法
Access-Control-Request-Headers: X-Custom-Header//发送额外的头部信息，以逗号分割
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
```

* 服务器的回应,在检查 origin，method 和 headers 字段后

```javascript
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://api.bob.com//（关键）
Access-Control-Allow-Methods: GET, POST, PUT//必须，返回的是所有支持方法，而不但是浏览器请求的那个方法，这样是避免多次预检请求
Access-Control-Allow-Headers: X-Custom-Header//如果浏览器有，则这个字段也是必须的
Access-Control-Max-Age: 1728000 //本次预检请求的有效期，单位是秒，允许缓存该条回应多少秒。在此期间，不用发出另一条预检请求。
Content-Type: text/html; charset=utf-8
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```

一旦服务器通过了预检请求，以后每次浏览器的请求都跟简单请求一样

* 优点：CORS 的优点：CORS 支持所有类型的 HTTP 请求，是跨域 HTTP 请求的根本解决方案。
