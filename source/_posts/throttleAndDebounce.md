---
title: 函数节流和防抖
date: 2017-04-10 20:26:06
tags: javascript
---

## 节流（throttle）

> 函数节流是指一定时间内只跑一次函数，就像是技能 cd，你再怎么按它一段时间内只能触发一次。

* 主要思想就是设置一个守卫（flag），如果 setTimeout 已经执行过了就不会创建新的 setTimeout

```javascript
let scrollFun = function(time) {
  let canRun = true; //初始化守卫变量
  return function() {
    if (canRun) {
      canRun = false;
      setTimeout(() => {
        console.log("throttle");
        //3s后变量才为true，才可以继续执行if条件
        canRun = true;
      }, time);
    }
  };
};
//3s执行一次
window.scroll = scrollFun(3000);
```

* 适用于窗口 resize

## 防抖(debounce)

> 函数防抖呢，就是频繁触发后，一旦空闲下来才开始执行这个函数。就像公交车司机，如果有人陆续刷卡，司机一直停车等待，直到所有人上来了才回开车。

* 主要思想就是如果频繁触发，就取消上一个，重新创建一个规定时长的 setTimeout

```javascript
let debounce = function(time) {
  let setTimeId = null;
  return function() {
    //取消上一个的
    clearTimeout(setTimeId);
    setTimeId = setTimeout(() => {
      console.log("debounce");
    }, time);
  };
};
//直到没有输入后，3s后才执行
$(el).input(dubounce(3000));
```

* 适用于输入框输入持续监听等
