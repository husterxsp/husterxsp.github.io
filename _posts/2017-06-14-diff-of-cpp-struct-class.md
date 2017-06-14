---
layout: post
title: C++ struct 和 class的区别
date: 2017-06-14
author: "Xsp"
catalog: false
tags:
    - C++
---

<!-- C语言中，数据和处理数据的操作（函数）是分开声明的。C++中则用独立的 抽象数据类型（Abstract data type, ADT）来实现。 -->

struct是C原有的，C++为了兼容C而支持了struct。什么时候该用struct或者class没有明确的界限。

语法角度的区别：
1. 默认继承权限不同。class是private，struct是public。
2. 成员的默认访问权限不同。class是private，struct是public。
3. struct不能作为模板参数，class可以。（这个有点不懂，为啥那个map的自定义排序可以用struct？）


参考：
+ [class与Struct 的区别](http://glgjing.github.io/blog/2015/02/18/shen-du-tan-suo-c-plus-plus-dui-xiang-mo-xing-:claass-yu-struct-de-qu-bie/)
+ [Struct和Class的区别 - csdn](http://blog.csdn.net/yuliu0552/article/details/6717915)
+ 《深度探索C++对象模型》 第1章
