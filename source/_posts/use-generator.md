---
title: Generator函数
date: 2017-05-23 15:04:59
tags: 'ES6'
comments: false
---
### Generator函数
> generator是生成器。generator可以看成是一个取号机，你拿一张票去向机器请求一个号码，你接受了你的号码之后，机器不会自动为你提供下一个。换句话说，取票机“暂停”直到另外一个人来请求另一个号码，此时它才会向后进行。

##### generator函数的创建
- ES6中用`function*`来创建生成器函数
- 当想要generator产生一个值之后暂停执行，需要使用到`yield`（生成）关键字，有点类似于return关键字（都可以返回一个值），只是yield返回值之后generator函数会暂停运行
``` javascript
function* ticketGenerate(){ //创建一个取号机
    yield 1;
    yield 2;
    yield 3;
}
```
- `yield*`是委派至另一个生成器函数 
``` javascript
function* anotherGenerator(i) {
  yield i + 1;
  yield i + 2;
  yield i + 3;
}

function* generator(i){
  yield i;
  yield* anotherGenerator(i);
  yield i + 10;
}

var gen = generator(10);

console.log(gen.next().value); // 10
console.log(gen.next().value); // 11
console.log(gen.next().value); // 12
console.log(gen.next().value); // 13
console.log(gen.next().value); // 20
```

##### generator函数的使用
- `ticketGenerate()`不会马上执行它的主体，而是会返回一个这个生成器函数的迭代器对象
- 当这个迭代器对象的next方法被调用时候，生成器函数的主体会执行到第一个yield表达式，该表达式定义了迭代器对象返回的值。
- next()返回一个对象：{value:1,done:false}，value属性是产出的值，done属性是表示生成器是否已经产出了它最后的值。
``` javascript
const ticket=ticketGenerate() //返回一个生成器对象
ticket.next() //{value: 1, done: false}
```

##### 影响generator的状态
- next()的另一个妙用是可以给它传递值，它会被视为generator中的yield语句的结果对待。
- 因此next是在generator运行过程中向其传递信息的方式。我们可以借此来重置取号机，将号码变为0。
``` javascript
function* ticketGenerator(){
    for(var i=0;true;i++){
        var reset=yield i;
        if(reset) {i=-1}
    }
}
var takeANumber = ticketGenerator(); 
console.log(takeANumber.next().value); //0  
console.log(takeANumber.next().value); //1 
console.log(takeANumber.next().value); //2 
console.log(takeANumber.next(true).value); //0 
console.log(takeANumber.next().value); //1     
```
