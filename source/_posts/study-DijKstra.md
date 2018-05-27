---
title: 读《图解算法》06-狄克斯特拉算法
date: 2018-05-27 19:45:02
tags: 图解算法
---

# 为什么有狄克斯特拉算法

> 这里的时间，换句话，我们给边加上了权重

* 之前 BFS 求出了最短路径，那么如果我们给边加上了时间呢？
* 发现最短路径并不是最快路径

# 步骤

* 找出最便宜的节点，就是说可以在最短时间内到达的节点（算法关键）
* 更新该节点的邻居的开销，检查是否有前往他们的更短路径，有就更新
* 重复这个过程，直到对图中的每个节点都这样做了
* 计算最终路径

# 术语

* 权重：狄克斯特拉算法用于每条边都有关联数字的图，这些关联数字叫做权重
* 带权重的图叫做加权图，计算最短路径用狄克斯特拉算法
* 不带权重的叫非加权图，计算最短路径用广度优先搜索
* 图还可能有环
* 负权边：权重是负的。
* 有负权边的情况，不能使用狄克斯特拉算法。因为该算法这样假设：对于处理过的节点，没有前往该节点的更短路径，只要在没有负权边才成立。应该使用贝尔曼-福德算法。

# 算法实现

* 需要三个散列表
  * 图的散列表
  * 节点的开销的散列表
  * 存储父节点的散列表
* 需要一个数组，用于记录处理过的节点，不用多次处理

```javascript
//如何表示加权图
graph = {};
graph["start"] = {};
graph["start"]["a"] = 6;
graph["start"]["b"] = 2;
graph["a"] = {};
graph["a"]["fin"] = 1;
graph["b"] = {};
graph["b"]["a"] = 3;
graph["b"]["fin"] = 5;
graph["fin"] = {}; //终点没有任何邻居
//
costs = {};
costs["a"] = 6;
costs["b"] = 2;
costs["fin"] = infinity;

//
parents = {};
parents["a"] = "start";
parents["b"] = "start";
parents["fin"] = None;
//算法
node = find_lowest_cost_node(costs)  //在未处理的节点中找出开销最小的节点
while node is not None:  //←------这个while循环在所有节点都被处理过后结束
    cost = costs[node]
    neighbors = graph[node]
    for n in neighbors.keys():  //←------遍历当前节点的所有邻居
        new_cost = cost + neighbors[n]
        if costs[n] > new_cost:  //←------如果经当前节点前往该邻居更近，
            costs[n] = new_cost  //←------就更新该邻居的开销
            parents[n] = node  //←------同时将该邻居的父节点设置为当前节点
    processed.append(node)  //←------将当前节点标记为处理过
    node = find_lowest_cost_node(costs)  //←------找出接下来要处理的节点，并循环
```
