---
title: 读《图解算法》04-散列表（hash table）
date: 2018-05-27 14:43:27
tags: 图解算法
---

# 为什么散列表会出现

* 场景：如果超市买东西结账的时候，售货员在一个本子里查找价格
* 如果是有序的（二分查找）还好，时间是 O（logn）,如果是无序（简单查找）的话，那就是 O（n）
* 如果直接有一个 O(1)的查找速度就好了

# 散列函数

> 将输入映射到数字

* 一致性：输入 apple 时得到的是 4，那么每次输入 apple 都必须是 4
* 不同性：不同的输入将得到不同的输出。如果输入 apple 和 yoki 都得到 4，那么这是一个不好的散列函数。

## 创造散列表

> 结合散列函数和数组

* 创建一个空数组
* apple 输入到散列函数，输出 3，然后把 apple 的价格存储到数组索引 3
* 接着各个类推，知道存完数组
* 然后我们要找 apple 的价格，就把 apple 输入交给散列函数，得到 3 去数组里面找

# 散列表的经典应用

> 查找，防止重复，用于缓存

## 查找

* 被用于大海捞针的查找
* 这个就不用细说了，类似的有 dns 解析

## 防止重复

> 场景：负责一个投票站，每个人只能投一票，如何避免重复投票

* 有一个方法是：有人来投票，就将它记录在一个投票名单里面，然后接下来的人都遍历这个投票名单，如果有就不能投票，但是这样就列表会越来越长，就会变慢
* 另外一种就是散列表，超快

## 缓存

* 浏览器缓存的数据存储在散列表里面
* 如果访问 facebook 的页面，会先检查散列表中是否存储了该页面
* 如果不在缓存中，才去访问服务器，然后把数据放到缓存里，这样下次有人访问就可以直接命中缓存

# 冲突

> 事实上不可能不同的输入都能获得不同的值

* 假设有一个数组包含 26 个位置，而我们使用的散列函数非常简单，按照字母表顺序分配位置
* apple 第一个，bear 第二个，接下来来了一个 banana，理应分到第二个，但是这个时候第二个位置已经是 bear 了
* 这个就叫冲突，解决冲突的办法有很多，简单的就是在这个冲突的位置存储一个链表，bear 末尾的指针指向 banana
* 所以散列函数很重要，好的散列函数将 key 均匀的映射到散列表的不同位置

## 如何避免冲突

* 较低的填装因子
  * 散列表包含的元素数：位置总数就是填装因子
  * 越小越好，满了只能调整长度，换一个更长的数组
  * 一个不错的经验规则就是，一旦填装因子大于 0.7，那么就要调整散列表的长度
* 良好的散列函数
  * SHA 函数s

# 散列表性能如何

## 平均情况

* 查找，插入，删除都是 O（1）,常量时间

## 最糟糕情况

* 查找，插入，删除都是 O（n）,线性时间

> 为什么会有两种情况，这是因为有了冲突可能会靠链表解决，n 个长度的链表，最好情况下没有冲突，就常量时间
