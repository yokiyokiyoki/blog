---
title: 动画了解一下(一)
date: 2017-11-23 14:32:05
tags: css
---

> 笼统的过一下动画的一些基本概念

## 基本概念

* 帧：在动画过程中，每一幅静止画面即为一“帧”
* 帧率：即每秒播放的静止画面的数量，单位是 fps(frame per second)或赫兹(Hz)
* 帧时长：即每一幅静止画面的停留时间，单位一般是毫秒
* 丢帧：在帧率固定的动画中，某一帧的时长远高于平均帧时长，导致其后续帧被挤压而丢失的现象

> 我们在显示器上看到的动画，每一帧变化都是系统绘制出来的（GPU 或者 CPU）。它的最高绘制频率受限于显示器的刷新频率（而非显卡，大多数都是 60Hz 或者 75Hz）

帧率越高，屏幕上图片闪烁感就越小，稳定性也就越高。人的眼睛不容易察觉 75Hz 以上刷新频率带来的闪烁感。

## 实现方式

> * js：通过定时器（setTimeout 和 setInterval）来改变元素样式，或者使用 requestAnimationFrame
> * css3:transition 和 animation
> * html5:使用 html5 提供的绘图方式(canvas/svg/webgl)

### requestAnimationFrame

* 这个 api 是浏览器用于定时循环操作的一个接口，类似于 setTimeout,主要用途是按帧对网页进行重绘
* 目的是为了让各种网页动画效果（dom 动画，canvas 动画，svg 动画，webgl 动画）能够有一个统一的刷新机制，从而节省系统资源，提供系统性能，改善视觉效果。
* 代码中使用这个 api，就是告诉浏览器希望执行一个动画，让浏览器在动画帧安排一次重绘。
* 其接受一个回调作为参数，这个回调函数会在浏览器重绘之前调用，由于功效只是一次性的，所以想实现连续的动效，需要递归调用。

```javascript
<div id="demo" style="position:absolute; width:100px; height:100px; background:#ccc; left:0; top:0;"></div>

<script>
var demo = document.getElementById('demo');
function render(){
    demo.style.left = parseInt(demo.style.left) + 1 + 'px'; //每一帧向右移动1px
}
requestAnimationFrame(function(){
    render();
    //当超过300px后才停止
    if(parseInt(demo.style.left) <= 300) requestAnimationFrame(arguments.callee);
});
</script>
```

* cancelAnimationFrame 用于取消重绘

```javascipt
var requestID = requestAnimationFrame(repeatOften);
cancelAnimationFrame(requestID);
```

> 使用 requestAnimationFrame 的优势如下

* 会把每一帧中的所有 dom 操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随着显示器的刷新频率（60 或者 75Hz）
* 在隐藏或不可见的元素中，将不会进行重绘或回流，这就意味着更少的 cpu，gpu 和内存使用量
* 所以它是性能优化版/专为动画量身打造的 setTimeout，不同的是它不是自己指定回调函数运行的时间，而是跟着浏览器内建的刷新频率来执行回调，这就能达到浏览器所能实现动画的最佳效果

### transition

* css 中的 transition 属性允许块级元素中的属性在指定的时间内平滑的改变

```javascript
transition:property duration timing-function delay
```

* property 是过渡效果的 css 属性的名称，比如 height
* duration 规定完成过渡效果需要多少毫秒或者秒
* timing-function 规定速度效果的曲线(linear/ease..)
* delay 规定过渡效果何时开始

### animation

* 跟 transition 比较，作用于元素本身而不是样式属性,可以使用关键帧的概念，应该说可以实现更自由的动画效果

```javascript
animation:name duration timing-function delay iteration-count direction
```

* name 是需要绑定到选择器的 keyframe 名称
* duration 规定完成动画所花费的时间，以秒或者毫秒算
* timing-function 是动画的速度曲线(line/ease..)
* delay 规定在动画开始之前的延迟
* iteration-count 规定动画应该播放的次数
* animation-direction 规定是否应该轮流反向播放动画(normal/alternate)

### Canvas

* html5 新增的元素，作为页面图形绘制的容器，可用于使用 js 中的脚本来绘制图形。
* 它可以用于绘制图形，制作照片，创建动画，甚至可以进行实时视频处理或者渲染
  > 它具有如下特点
* 依赖分辨率，基于位图
* 不支持事件处理器
* 弱的文本渲染能力
* 能够以.pbg 或者.jpg 格式保存结果图像
* 最适合图像密集型的游戏，其中的许多对象会被频繁重绘

### SVG

* scalable vector graphics==可缩放矢量图形，用来定义用于网络的基于矢量的图形，使用 xml 格式定义图像
  > 它具有如下特点
* 不依赖分辨率，基于矢量图
* 支持事件处理器
* 最适合带有大型渲染区域的应用程序，如谷歌地图
* 复杂度高会减慢渲染速度（任何过度使用 dom 的应用都不快）
* 不适合游戏应用

> svg 示例

```javascript
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <rect
    x="50"
    y="20"
    rx="20"
    ry="20"
    width="150"
    height="150"
    style="fill:red;stroke:black;stroke-width:5;opacity:0.5"
  />
</svg>
```

* svg 代码以 svg 根元素开始。
* width 和 height 设定 svg 文档的高度和宽度
* version 指定所使用的 svg 版本
* xmlns 定义 svg 命名空间

### webgl

* webgl 使得网页在支持 canvas 标签的浏览器中，不需要安装任何插件，便可以使用基于 OpenGL ES 2.0 的 api 在 canvas 中进行 3D 渲染。
* webgl 由 js 控制代码，和在计算机的图形处理单元（GPU）中执行的特效代码(shader code,渲染代码)组成

## 常用的动画库

* [Ani.js--基于 css 动画的生命处理库](http://anijs.github.io/)
* [Dynamics.js--创建具有物理运动效果动画的 js 库](http://dynamicsjs.com/)
* [Animate.css--齐全的 css3 动画库](https://daneden.github.io/animate.css/)
* [Three.js--快速搭建 webgl 项目](https://threejs.org/examples/#webgl_animation_cloth)
