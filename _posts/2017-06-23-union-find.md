---
layout: post
title: 并查集
date: 2017-06-23
author: "Xsp"
catalog: true
tags:
    - Algorithm
---

[coursera课程笔记](https://www.coursera.org/learn/algorithms-part1/home/week/1)

解决算法的一个步骤：
1. 问题建模，找到解决算法（可以使暴力解）。
2. 时间复杂度？空间复杂度？原因？（可以考虑用空间换时间，用时间换空间）
3. 优化
4. 注意边界条件（空值，极大/小值）

[关于并查集 - 维基百科](https://zh.wikipedia.org/wiki/%E5%B9%B6%E6%9F%A5%E9%9B%86)

主要有两个操作：
+ Find：确定元素属于哪一个子集。它可以被用来确定两个元素是否属于同一子集。
+ Union：将两个子集合并成同一个集合。

连通分量：互相连接的对象的集合（set）

### 快速查找（quick find）

数据结构：简单的对象索引整数数组，如果两个点是连通的，则在数组中的值一样。

查找很高效，但是连接的效率比较低（遍历数组将连通的数组元素的值置为相同）。

### 快速合并（quick union）
采用“懒策略”，尽量避免计算直到不得不进行计算

构造树，但是依然有问题，树的高度可能很高，这样查找根节点的效率就很低，

### 带权快速合并（weighted quick union）
通过将小树放在大树下，避免得到很高的树。

### 路径压缩（path compression）
查找根节点的过程中，顺带将树展平
