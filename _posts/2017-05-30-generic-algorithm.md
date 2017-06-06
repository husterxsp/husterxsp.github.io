---
layout: post
title: 泛型算法
date: 2017-05-30
author: "Xsp"
catalog: true
tags:
    - C++
---
《C++ primer》th5 学习笔记 第10章

### 概述
泛型算法是一些经典算法的公共接口，可适用于多种容器，大多数定义在algorithm头文件，另外在numeric头文件定义了一组数值泛型算法。这些算法运行于迭代器之上。

### 初识泛型算法

#### 只读算法

+ accumulate()，前两个参数指定需要求和的范围，第三个参数决定使用的加法运算符和返回值类型。
如下的代码则是分别对整数和字符串求和。需要注意对字符串求和时显式的创建了string，若是直接传入空串“”会出错。因为对于空串，用于保存的对象的类型是 const char* ，它没有 + 运算符。
```cpp
int sum = accumulate(v.begin(), v.end(), 0);
string sum = accumulate(v.begin(), v.end(), string(""));
// error: const char* 上未定义+运算符
string sum = accumulate(v.begin(), v.end(), "");
```

+ equal()，用于比较两序列是否相等
```cpp
equal(v1.begin(), v1.end(), v2.begin());
```

#### 写容器算法

+ fill()，对序列重新赋值。fill_n()，对迭代器之后的n个元素赋值。
```cpp
fill(v.begin(), v.end(), 0);
fill_n(v.begin(), n, 0);
```
+ back_inserter()，插入迭代器。接受一个容器的引用，返回与容器绑定的插入迭代器。通过该迭代器赋值时，会调用push_back()插入元素。
```cpp
vector<int> v;
auto it = back_inserter(v);
*it = 1;
```
+ copy()，拷贝算法。

#### 重排序算法

+ sort()，重排序。unique()，使重复的元素出现在vector开始部分。
```cpp
sort(v.begin(), v.end());
// 返回指向不重复区域之后的一个位置的迭代器
auto end_unique = unique(v.begin(), v.end());
v.erase(end_unique, v.end());
```
需要注意这些标准库算法是对迭代器操作，不能直接添加或删除元素。所以最后还得通过erase来操作。

### 定制操作

#### 向算法传入函数

谓词，可调用表达式，返回一个能用作条件的值。标准库算法用的谓词包括一元谓词和二元谓词，即接受的参数为1个或两个。接受二元谓词参数的sort用这个谓词代替 < 操作。
```cpp
bool compareWord(const string &s1, const string &s2) {
    return s1.size() < s2.size();
}
sort(v.begin(), v.end(), compareWord);
```

stable_sort() 稳定排序，排序之后值相等的元素保持原来乱序时的顺序。

#### lambda表达式

一个可调用的代码单元，未命名的内联函数。lambda表达式的形式如下，捕获列表是lambda所在函数重定义的局部列表。lambda表达式应至少包括捕获列表和函数体。
```cpp
// [捕获列表] (参数列表) -> 返回值类型 {函数体}。
auto f = []{return 1;};
```
#### lambda 捕获和返回

当定义一个lambda时，编译器生成一个与lambda对应的新的(未命名的)类类型。当向一个函数传递一个lambda时，同时定义了一个新的类型和该类型的对象。

##### 值捕获和引用捕获

```cpp
size_t v1 = 1;
// 值捕获，捕获的变量在创建时拷贝。
auto f1 = [v1]{return v1;};
// 引用捕获
auto f2 = [&v1]{return v1;};
```
使用引用捕获时应保证在lambda执行时，变量存在。

捕获普通变量，如int、string或其他非指针类型，通常可以是简单的值捕获。
如果捕获指针或迭代器，或采用应用捕获方式，就必须保证在lambda执行时，绑定的对象依然存在。

##### 隐式捕获
捕获列表直接用&或=，&表示引用捕获，=表示值捕获，编译器会根据函数体中的变量推断捕获列表。

##### 混合使用
混合使用隐式或显示捕获，捕获列表第一个元素应当是&或=，指定隐式捕获类型。另外显式捕获的类型必须和隐式的类型不一样。

##### 可变lambda
对于值捕获的变量，lambda默认不能改变其值。如要需要改变，则需要加上mutable关键字
```cpp
auto f = [v1]()mutable{return ++v1};
```

##### 指定lambda返回类型

默认情况下，若lambda包含return之外的任何语句，则编译器假定词lambda返回void。而此时若函数体有返回值则会编译错误。此时应当在lambda的表示形式中显式的声明返回值类型。

#### 参数绑定
...待看

### 再探迭代器
除了容器的迭代器，标准库iterator中定义了额外的几种迭代器，
+ 插入迭代器(insert iterator)
+ 流迭代器(stream iterator)
+ 反向迭代器(reverse iterator)
+ 反向迭代器(move iterator)

... 待看
