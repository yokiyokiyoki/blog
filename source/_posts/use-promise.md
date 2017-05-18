---
title: 使用promise
date: 2017-05-17 15:16:13
tags: "ES6"
---
### Promise
> Promise作为ES6的特性，可以很好地创建异步工作流

- Promise是构造函数
- Promise只有三种状态（pending，resolve，reject），pending是刚创建Promise实例的状态，然后一旦转为resolve或者reject，就不可变更

#### 创建Promise
> 以封装xhr为例子

``` javascript
function getURL(url){
    return new Promise((resolve,reject)=>{ //Promise构造函数接受一个函数，这个函数接受两个参数
        let xhr = new XMLHttpRequest()
        xhr.open('GET',url,true) 
        xhr.onload=function(){
            if(xhr.status==200){
                resolve(JSON.parse(xhr.responseText)) //如果成功响应，那么把这个值填充到resolve
            }else{
                reject(new Error(xhr.statusText)) //把该错误对象填充到reject里面
            }
        }
        xhr.onError=function(){
            reject(new Error(xhr.statusText)) //把该错误对象填充到reject里面
        }
        xhr.send(null)
    })
}
```

- reject必须是接受一个错误对象实例，比如`new Error('12')`里面是什么类型的都可以

#### 使用Promise
> 使用上面封装xhr的getURL的函数，该函数返回一个Promise实例

- Promise的实例方法有两个：then和catch，这个两个方法都返回新的Promise对象
- then有两个参数。then(onfilled,onrejected)，第一个是参数值resolve来填充，第二个是reject来填充
- catch实则是then(undefined,onrejected)的别名，但是一般情况下都是用这个
- then检测不了自身Promise对象onfilled出了异常，而catch是基于对上次的then产生的promise对象

``` javascript
let url1='http://azu.github.io/promises-book/json/comment.json',
    url2='http://azu.github.io/promises-book/json/people.json'
getURL(url1).then((val)=>{
    console.log(val)  //这里是onfilled填充
}).catch(error=>{
    console.log(error) //这里是onrejected填充
})
```

##### Promise chain获取上次promise的返回值
> 由于Promise 链式调用的时候，每个Promise都是独立的。但是可以通过return值来传给下一个Promise，因为return了值是填充了当前Promise对象的resolve
``` javascript
promise.then(val=>{return val}).then(val=>{console.log(val)})
```
##### Promise静态方法
- Promise.resolve(42) === new Promise((resolve,reject)=>resolve(42)) ,是它的语法糖
- Promise.reject(new Error(42)) === new Promise((resolve,reject)=>reject(new Error(42))) 是它的语法糖
- 如果有多个Promise同时需要判断才能进行下一步，可以用Promise.all([promise1,promise2])。这个then方法返回的参数也是一个数组，跟promise1和promise2的顺序一样。需要注意的是，all是并行执行的。
- Promise.race([promise1,promise2]) 则和数组的some方法很像，只要有一个promise对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理。

``` javascript
promise.all([getURL(url1),getURL(url2)]).then(function(result){
    console.log(result)  //[getURL(url1)的结果，getURL(url2)的结果]
})
promise.race([getURL(url1),getURL(url2)]).then(function(value){
    console.log(value)  //一旦这两个promise对象有一个完成了onfilled或onrejected状态，那么就会进行后续的then，所以这里then的参数只有一个value。但不并不会影响其他promise对象的进行，只是它们的结果值不会出现在then的里面
})
```
