---
title: 异常
date: 2017-05-18 15:59:06
tags: "ES6"
---
### throw
> 用来抛出一个异常，throw expression

- throw 12 //抛出数值12的异常
- throw 'err' //抛出字符串'err'的异常
- throw {name:'异常的名字',message:'异常的信息',stack:'一般还有个函数栈'} //抛出对象的异常，一般这个对象包含名字，信息，栈。可以试一下throw new Error('123')

也可以创建自定义错误
``` javascript
function UserException(message){
    this.name='UserException';
    this.message=message
}
throw new UserException('用户消息错误')
```

### try..catch大法
> 配合throw能找出自己程序的异常

``` javascript
function printValue(value){
    if(value==1){
        throw new Error('值是1错误')
    }else{
        console.log(value)
    }
}
try{
    printValue(1)
}catch(e){
    console.log(e.message,e.name,e.stack)
}
```