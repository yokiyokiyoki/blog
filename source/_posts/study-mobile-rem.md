---
title: 关于移动端rem适配方案
date: 2019-08-16 15:18:26
tags: 前端
---
# 基本概念

- 物理像素(physical pixel):一个物理像素是显示器(手机屏幕)上最小的物理显示单元,pp 只与硬件设备有关。屏幕可视宽为 320 像素，非 retina 屏的 pp 是 320，retina 屏的则至少为 640
- 设备独立像素(device pixel ratio )：dpr是个虚拟单位，在 web 世界中也称为 css 像素[逻辑像素]，屏幕可视宽为 320 像素，dip 也就是 320 像素（window.devicePixelRatio ）
- 设备像素比(density-independent pixel)：设备像素比 = 物理像素 / 设备独立像素 // 在某一方向上，x方向或者y方向

## 部分机型示例

- iphone5/SE
    - 独立像素(CSS像素)：320*568
    - 物理像素(分辨率):640*1136
    - 设备像素比(dpr):2
- iPhone 6/7/8
    - 独立像素(CSS像素)：375*678
    - 物理像素(分辨率):750*1334
    - 设备像素比(dpr):2
- iPhone 6/7/8 Plus
    - 独立像素(CSS像素)：414*736
    - 物理像素(分辨率):1920*1080(1242x2208)
    - 设备像素比(dpr):3

## 关系

![](https://images2018.cnblogs.com/blog/1033257/201809/1033257-20180906102122437-1393808833.png)

pt和px的关系就是—— 1pt(独立像素) 里面有几个像素（物理像素）点 （比如 1pt里面有1个px，也可以有2个，3个，分别对应上图的@1x,@2x,@3x） 

- 所以为什么设计稿640px和750px要除以2，就是因为设计师给的640px和750px是物理像素， 而我们在浏览器模拟调试移动端的时候看到的像素是独立像素。
- 可能会有这样一个疑问，1pt里面像素点越多肯定越清晰，那为什么没有看到设计师给1242px的设计稿呢？ 　　听一个大神说是因为人用肉眼睛能分辨出的最大分辨率就是@2x即750*1334，而@3x已经超过人肉眼分辨的极限了，没必要。

# 适配方案

- 禁止缩放
```js
<meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />
```
- 引用淘宝的flexible.js方案

- 使用postcss-pxtorem在构建过程中将px转为rem

```js
postcss: [require('postcss-pxtorem')({
    rootValue: 37.5, // 设计稿是750px
    propList: ['*'],
    unitPrecision: 3,        // 指定`px`转换为`rem`单位值的小数位数（很多时候无法整除）
    selectorBlackList: ['.ignore ','.hairlines'], //  指定不转换为`rem`单位的类，可以自定义，可以无限添加,建议定义一至两个通用的类名
    minPixelValue: 2       // 设置要替换的最小像素值(大于或等于`2px`才转换为`rem`单位)，你也可以设置为你想要的值
})]
```

## 其他

### 处理1像素边框

在rem方案中，如果没有特殊处理，1px的线条在 dpr 大于 1 的设备会比较粗。根本原因是1px可能会占用两个物理像素点或以上。
可以采用 transform: scale(0.5);来解决，在上面的selectorBlackList添加'.ui-hairline'
