---
layout: post
title: 关联容器map,set
date: 2017-05-28
author: "Xsp"
catalog: false
tags:
    - C++
---
《C++ primer》学习笔记

1. 用于高效查找和访问，主要包括map、set、multimap、multiset，以及加上unordered前缀的无序的四个。
2. map中元素是一个pair类型的对象，该对象用first保存key，用second保存value。
3. 对于有序容器，关键字类型必须定义元素比较的方法，默认用<比较关键字，可以加入自定义的比较函数。如果map的key没法用<比较又没自定义比较函数，则会报错。
4. key_type，value_type，set的这两个类型一样。map的value_type是pair，表示保存的元素的类型，另外map还有一个mapped_type表示映射的值的类型。
5. 关联容器的迭代器是是一个类型为value_type的值的引用。该迭代器的关键字key不可改变。set的迭代器则是const的。
6. 添加元素emplace和insert，insert传入对象，emplace传入用于构造对象的参数。emplace会避免临时变量的构造<sup>[[1](http://blog.guorongfei.com/2016/03/16/cppx-stdlib-empalce/#fn2)]</sup>
7. 删除元素erase。
8. map下标操作：c[k]，若k不存在，则添加并初始化。at操作：c.at(k)，若k不存在则抛出out_of_range异常。下标操作的副作用，关键字不存在则插入。
9. 访问元素find，count。lower_bound(k)返回迭代器，指向第一个关键字大于等于k的元素。upper_bound(k)则是指向第一个关键字大于k的元素。
10. 遍历multimap和multiset， count获取数量，find找到第一个再迭代。也可以直接用lowe_bound和upper_bound。第三种办法是用equal_range()，该函数返回一个迭代器pair。
11. 无序容器在存储上组织为一组桶。每个桶保存一个或多个元素。无序容器使用一个哈希函数将元素映射到桶。
