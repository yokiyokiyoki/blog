---
title: 模板字符串的替换
date: 2017-05-11 11:04:02
tags: javascript
---

> 看到 vue 模板里都是用{{}}包含变量，然后替换成变量，觉得比字符串拼接实在是好太多了

* es6 的字符串模板用起来实在是舒服，所以简单用{{}}实现一下

```javascript
// 首先 str.replace(regexp|substr, newSubStr|function) ，replace 可以传递一个函数
str.replace(reg,function(match,key)=>{})
//正则匹配{{内容}}，应该是/\{\{(.*?)\}\}/，用\转义{和}
/\{\{(.*?)\}\}/g;
```

* **.\*?**是正则的固定搭配用法，就是表示非贪婪模式，尽可能匹配少的意思
* **.\***则表示贪婪模式

```javascript
源字符串：aa<div>test1</div>bb<div>test2</div>cc

正则表达式一：<div>.*</div>

匹配结果一：<div>test1</div>bb<div>test2</div>(从头部匹配到尾部，从第一个div到最后的那个</div>)

正则表达式二：<div>.*?</div>

匹配结果二：<div>test1</div>（这里指的是一次匹配结果，不使用/g，所以没包括<div>test2</div>）
```

* 所以有上面的简单铺垫，可以简单写出

```javascript
function render(template, context) {
  return template.replace(
    /\{\{(.*?)\}\}/g,
    (match, key) => context[key.trim()]
  );
}
//可以使用trim去掉前后空格
const template = "我是{{name   }}，现在{{age   }}岁";
const context = { name: "yoki", age: "20" };
console.log(render(template, context));
```
