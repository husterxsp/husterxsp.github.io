---
layout: post
title: 顺序容器
date: 2017-05-29
author: "Xsp"
catalog: false
tags:
    - C++
---
《C++ primer》th5 学习笔记 第9章

1. 标准库顺序容器：
    + vector 可变大小数组
    + deque 双端
    + list 双向链表
    + forward_list 单项链表
    + array 固定大小数组
    + string 与vector类似
2. 迭代器范围，左闭合区间[begin， end). rbegin/rend反向迭代器. cbegin/cend const迭代器
3. vector对象如何增长？当需要获取新的内存空间时，vector通常会分配比需求更大的内存空间，作为备用。当空间用完后，会将已有元素从就旧位置移动到新位置，然后添加元素，释放旧存储空间。注意元素必须连续存储。
4. 顺序容器用operator[] 访问，数组越界并不会报错，只不过行为未定义。
5. string操作
    + 子串 s.substr(pos， n)
    + 搜索 find
    + 比较 s.compare(args)
    + 数值转换 to_string()， stoi()，另外注意atoi()方法是C里的，atoi(str.c_str())先将C++ string 装换为C Array 再转换为int。
6. 容器适配器，接受一种已有的容器类型，使行为看起来像另外一种不同的类型。
    + stack，queue，默认基于deque实现
    + priority_queue，默认基于vector实现
