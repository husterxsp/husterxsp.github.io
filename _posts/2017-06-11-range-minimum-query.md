---
layout: post
title: 范围最小值问题
date: 2017-06-11
author: "Xsp"
catalog: true
tags:
    - Algorithm
---

范围最小值问题（Range Minimum Query, RMQ）。给定一个数组A[1, n]，求A[i，j]的最小值。
这里假定数组A的元素不会变化（如果变化咋办？）。

[范围最值查询-维基百科](https://zh.wikipedia.org/wiki/%E8%8C%83%E5%9B%B4%E6%9C%80%E5%80%BC%E6%9F%A5%E8%AF%A2)

### Sparse-Table（ST）算法
预处理时间O(nlogn)，查询O(1)。

令$$d(i，j)$$ 表示从$$ i $$开始，长度为$$ 2^j $$的一段元素中的最小值。
利用动态规划，状态转移方程是：$$d(i，j) = min\{d(i， j - 1)，d(i + 2^{j - 1}，j - 1)\}$$。

注意$$2^j \leq n，j \leq log n$$。故d数组的元素个数不超过$$nlogn$$。每一项可在常数时间计算完，故总时间为$$O(nlogn)$$。


以下为预处理代码，注意嵌套的for循环的顺序，j在外层，i在内层。
```cpp
void RMQ_init(vector<int> A) {
    int n = A.size();
    for (int i = 0; i < n; i++) d[i][0] = A[i];
    for (int j = 0; (1<<j) <= n; j++)
        for (int i = 0; i + (1 << j) - 1 < n; i++)
            d[i][j] = min(d[i][j - 1], d[i + (1<<(j - 1))][j - 1]);
}
```

查询代码如下。令k为满足$$2^k \leq j-i+1$$的最大整数。则两个待比较的区间范围分别是以i开始和以j结尾的长度$$2^k$$的区间。即$$[i,i+2^k-1],[j-2^k+1,j]$$，两个区间有重复也没关系，需要确保能够覆盖[i，j]整个区间。如果是累加之类的问题，则注意不能重复。
```cpp
int RMQ(int i, int j) {
    int k = 0;
    while (i + (1 << (k + 1)) - 1 <= j) k++;
    return min(d[i][k], d[j - (1 << k) + 1][k]);
}
```

参考：
+ [http://blog.csdn.net/niushuai666/article/details/6624672/](http://blog.csdn.net/niushuai666/article/details/6624672/)
+ 《算法竞赛入门经典-训练指南》
