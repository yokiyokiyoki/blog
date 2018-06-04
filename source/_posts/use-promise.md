---
title: 使用promise
date: 2017-05-17 15:16:13
tags: javascript
comments: false
---

### Promise

> Promise 作为 ES6 的特性，可以很好地创建异步工作流

* Promise 是构造函数
* Promise 只有三种状态（pending，resolve，reject），pending 是刚创建 Promise 实例的状态，然后一旦转为 resolve 或者 reject，就不可变更

#### 创建 Promise

> 以封装 xhr 为例子

```javascript
function getURL(url) {
  return new Promise((resolve, reject) => {
    //Promise构造函数接受一个函数，这个函数接受两个参数
    let xhr = new XMLHttpRequest();
    xhr.open("GET", url, true);
    xhr.onload = function() {
      if (xhr.status == 200) {
        resolve(JSON.parse(xhr.responseText)); //如果成功响应，那么把这个值填充到resolve
      } else {
        reject(new Error(xhr.statusText)); //把该错误对象填充到reject里面
      }
    };
    xhr.onError = function() {
      reject(new Error(xhr.statusText)); //把该错误对象填充到reject里面
    };
    xhr.send(null);
  });
}
```

* reject 必须是接受一个错误对象实例，比如`new Error('12')`里面是什么类型的都可以

#### 使用 Promise

> 使用上面封装 xhr 的 getURL 的函数，该函数返回一个 Promise 实例

* Promise 的实例方法有两个：then 和 catch，这个两个方法都返回新的 Promise 对象
* then 有两个参数。then(onfilled,onrejected)，第一个是参数值 resolve 来填充，第二个是 reject 来填充
* catch 实则是 then(undefined,onrejected)的别名，但是一般情况下都是用这个
* then 的第二个参数 检测不了自身 Promise 对象 onfilled 出了异常，而 catch 是基于对上次的 then 产生的 promise 对象，[看这里](https://www.kancloud.cn/kancloud/promises-book/44255)

```javascript
let url1 = "http://azu.github.io/promises-book/json/comment.json",
  url2 = "http://azu.github.io/promises-book/json/people.json";
getURL(url1)
  .then(val => {
    console.log(val); //这里是onfilled填充
  })
  .catch(error => {
    console.log(error); //这里是onrejected填充
  });
```

##### Promise chain 获取上次 promise 的返回值

> 由于 Promise 链式调用的时候，每个 Promise 都是独立的。但是可以通过 return 值来传给下一个 Promise，因为 return 了值是填充了当前 Promise 对象的 resolve

```javascript
promise
  .then(val => {
    return val;
  })
  .then(val => {
    console.log(val);
  });
```

##### Promise 静态方法

* Promise.resolve(42) === new Promise((resolve,reject)=>resolve(42)) ,是它的语法糖
* Promise.reject(new Error(42)) === new Promise((resolve,reject)=>reject(new Error(42))) 是它的语法糖
* 如果有多个 Promise 同时需要判断才能进行下一步，可以用 Promise.all([promise1,promise2])。这个 then 方法返回的参数也是一个数组，跟 promise1 和 promise2 的顺序一样。需要注意的是，all 是并行执行的。
* Promise.race([promise1,promise2]) 则和数组的 some 方法很像，只要有一个 promise 对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理。

```javascript
promise.all([getURL(url1), getURL(url2)]).then(function(result) {
  console.log(result); //[getURL(url1)的结果，getURL(url2)的结果]
});
promise.race([getURL(url1), getURL(url2)]).then(function(value) {
  console.log(value); //一旦这两个promise对象有一个完成了onfilled或onrejected状态，那么就会进行后续的then，所以这里then的参数只有一个value。但不并不会影响其他promise对象的进行，只是它们的结果值不会出现在then的里面
});
```
