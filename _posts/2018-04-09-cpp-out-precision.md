---
layout: post
title: C++ 输出时精度控制
date: 2018-04-09
author: "Xsp"
catalog: true
tags:
    - C++
    - Algorithm
---
问题 [problem A](https://code.google.com/codejam/contest/6274486/dashboard)

在输出的时候没有控制精度, 直接 cout 的话 double 默认保留6位有效数字, 结果 Incorrect

可以设置精度如下：
```cpp
double a = 1.123456789;
// 保留10位有效数字
cout << setprecision(10);
cout << a << endl;
// 也可以这样写
cout << setprecision(10) << a << endl;

// 保留10位小数
printf("%.10f\n", a);
// . 表示精度，不加 . 10则表示宽度
printf("%.10f\n", a);

// 加上fixed 表示输出固定小数位
cout.setf(ios::fixed);
cout << setprecision(10);

// showpoint在位数不够时会补0
cout.setf(ios::showpoint);
```


参考：
+ [https://blog.csdn.net/yss28/article/details/53538063](https://blog.csdn.net/yss28/article/details/53538063)
+ [https://stackoverflow.com/questions/4264127/correct-format-specifier-for-double-in-printf](https://stackoverflow.com/questions/4264127/correct-format-specifier-for-double-in-printf)
+ [https://blog.csdn.net/u011321546/article/details/9293547](https://blog.csdn.net/u011321546/article/details/9293547)
