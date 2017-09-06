---
layout: post
title: 凸包算法
date: 2017-08-01
author: "Xsp"
catalog: true
tags:
    - Algorithm
---

### 定义
凸包是包所有点的最小的凸状集合

### 葛立恒（Graham）扫描法

1. 以逆时针/左转的方式穿过凸包
2. 选择y轴坐标最小的点（最低点）作为p点，即扫描的起始点，会发现从p点到其他每个点的向量的极角值递增。

难点：
1. 如何找到最低点？对y坐标排序
2. 如何根据极角大小排序，以及如何比较这些点？
3. 如何判断两点间是否是逆时针旋转？

计算凸包的主要部分就是排序。有一个好的排序算法，就可以得到一个好的凸包算法。

### 逆时针判断（counterclockwise, ccw）
给定三个点a、b、c, 如何判断a->b->c是否是逆时针？
通过计算：<br>
$$
\begin{equation}
\begin{aligned}
\overrightarrow{ab}\times\overrightarrow{ac}
&=(b - a)\times(c - a) \\
&=(b.x - a.x, b.y - a.y)\times(c.x - a.x, c.y - a.y)\\
&=(b.x - a.x) * (c.y - a.y) - (b.y - a.y) * (c.x - a.x)
\end{aligned}
\end{equation}
$$

1. 大于0, 逆时针
2. 小于0, 顺时针
3. 等于0, 共线

（叉乘是用来在坐标系中判断方向的，右手螺旋。。）

参考：
+ https://www.coursera.org/learn/algorithms-part1/lecture/KHJ1t/convex-hull
+ https://leetcode.com/contest/leetcode-weekly-contest-32/problems/erect-the-fence/
