---
layout: post
title: C/C++ long和long long的区别
date: 2018-02-02
author: "Xsp"
catalog: true
tags:
    - C++
---

C/C++ 里有long和long long。区别如下：

64bit机器上，long和long long都是8byte，32bit机器上，long是4byte，long long是8byte。

所以一般情况下写long就可以了，毕竟现在基本没有32bit的机子了。

sizeof(char) == 1

sizeof(char) <= sizeof(short) <= sizeof(int) <= sizeof(long) <= sizeof(long long)


Xcode 中可以通过设置 项目->Build settings -> Architectures 来测试32bit
