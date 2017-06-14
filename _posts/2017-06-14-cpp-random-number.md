---
layout: post
title: C++随机数
date: 2017-06-14
author: "Xsp"
catalog: false
tags:
    - C++
---

通过简单的C库函数rand来生成随机数，此函数生成均匀分布的伪随机数，每个随机数的范围在0和一个系统相关的最大值（至少为32767）之间（最大值是多少？INT_MAX？）

首先初始化随机数种子，使程序每次运行时生成不同的随机序列。
因为rand() 的范围是0~max，所以通过取余的方式来获取需要的范围内的随机数
```cpp
srand(time(NULL));
rand() % 100;
```

其他：
+ [给定一个能生成1到5随机数的函数，如何利用它来生成1到7的随机数?](http://www.code123.cc/959.html)
+ 《C++ Primer th5》 17.4 Random Numbers待看
