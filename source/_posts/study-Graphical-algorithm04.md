---
title: 读《图解算法》03-快速排序
date: 2018-05-06 19:25:46
tags: 图解算法
---

## 分而治之（divide and conquer）

* 缩写就是 D&C
* 一种著名的递归式问题解决办法
* 快速排序是一个重要的 D&C 算法

### 步骤

* 找出基线条件，这种条件应该尽可能简单
* 不断将问题分解（或者说缩小规模），直到符合基线条件

### 场景

> 给定一个数组[2,4,6]，然后相加，并返回结果，不使用循环

* 第一步找到基线条件，最简单的数组是怎样的？如果数组不包含一个元素或者只有一个元素，那么计算总和就容易了，这就是基线条件
* 每次递归调用都必须离空数组更近

```javascript
function sum(arr) {
  if (arr.length == 1) {
    return arr[0];
  } else {
    //别忘了递归记录了状态
    return arr.shift() + sum(arr);
  }
}
```

## 快速排序

> 使用快排对数组进行排序

* 对于排序算法来说，最简单的数组是怎么样的？没错，就是根本不需要排序的一个元素的数组或者空数组

### 原理

> [33,15,10]

*  首先在数组中选择一个元素，这个元素被称为基准值（pivot），等等介绍如何选择合适的基准值，这里选择第一个元素 33
* 找出比基准值小的元素和比他大的元素，这叫分区
* 现在我有：一个由所有小于基准值的数字组成的子数组，基准值，一个由所有大于基准值的数字组成的子数组
* 然后再对子数组进行排序，然后合并结果就能得到有序数组：quicksort([15,10])+[33]+quicksort([])

```javascript
function quicksort(arr) {
  if (arr.length < 2) {
    //基线条件：为空或者包含一个元素的数组有序
    return arr;
  } else {
    let pivot = arr[0]; //递归条件
    let less = [],
      greater = [];
    //子数组
    arr.forEach(item => {
      if (item < pivot) {
        less.push(item);
      }
      if (item > pivot) {
        greater.push(item);
      }
    });
    return [...quicksort(less), pivot, ...quicksort(greater)];
  }
}
```

### 最糟情况和平均情况

* 平均情况下是 O（n\*logn）

  > 有一种算法叫合并排序也是这个，但是为什么不用这个呢，是因为大 O 表示法有个常量（也被叫做固定时间量）被省去了，如果两个算法的大 O 运行时间不一样，那么常量无关紧要，参考简单查找和二分查找。如果一样的话，那么常量的影响非常大。两个函数，后面那个我每读一个元素，睡一秒种，但是其实两个函数速度一样，但是前面所需的时间更少。同理可得，快排所需的时间更少，而且相对于遇上最糟情况，它遇上平均情况的可能性大得多。

* 最糟糕就是与选择排序一样慢，O（n^2）

#### 场景

* 比如[1,2,3,4,5,6,7]
* 如果从第一个数作为基准值，数组没有被切成两半，这将导致调用栈很长，栈长 n 个，而且最上面那层都涉及 n 个元素，然后类推 n-1 个元素，最终得出 n\*n\*1/2 个操作，去掉常数，也就是 n^2
* 如果总是将中间的元素作为基准值，数组分成两半，最佳情况下,栈长为 logn 个,然后每次也是涉及 O（n）个元素（去掉常量），也就是 n\*logn。而且最佳情况也是平均情况，只要每次随机地选择一个数组元素作为基准值，那么平均运行时间就是 O（nlogn）

```javascript
function quicksort(arr) {
  if (arr.length < 2) {
    //基线条件：为空或者包含一个元素的数组有序
    return arr;
  } else {
    //随机选择索引
    let index = randomNum(0, arr.length - 1);
    let pivot = arr[index]; //递归条件
    let less = [],
      greater = [];
    //子数组
    arr.forEach(item => {
      if (item < pivot) {
        less.push(item);
      }
      if (item > pivot) {
        greater.push(item);
      }
    });
    return [...quicksort(less), pivot, ...quicksort(greater)];
  }
}
//随机数
function randomNum(minNum, maxNum) {
  switch (arguments.length) {
    case 1:
      return parseInt(Math.random() * minNum + 1, 10);
      break;
    case 2:
      return parseInt(Math.random() * (maxNum - minNum + 1) + minNum, 10);
      break;
    default:
      return 0;
      break;
  }
}
```
