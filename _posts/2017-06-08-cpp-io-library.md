---
layout: post
title: C++ IO 库
date: 2017-06-08
author: "Xsp"
catalog: true
tags:
    - C++
---

《C++ primer》th5 学习笔记 第8章

基本IO库设施：
+ istream： 输入操作。
+ ostream： 输出操作。
+ cin：一个istream对象，从标准输入读数据。
+ cout：一个ostream对象，向标准输出写数据。
+ cerr: osstream对象，用于输出程序错误消息。
+ `>>` 运算符: 从一个istream对象读输入数据。
+ `<<` 运算符: 向一个ostream对象写输出数据。
+ getline(): 从istream读取一行数据存入string。

### IO类

|  头文件     | 类型 |
| ---------- | --- |
| iostream   |  读写流的基本类型 |
| fstream    |  读写命名文件的基本类型 |
| sstream    |  读写内存string对象的基本类型|


### string 流

sstream 头文件 定义了三个类型来支持内存IO

| 类型 | 功能 |
|-|-|
| istringstream | 从string读取数据 |
| ostringstream | 向string写入数据 |
| stringstream  | 既可从string读数据也可向string写数据|


#### istringstream
```cpp
// 使用字符串初始化
istringstream myStr("hello world");
//把字符串"hello world"存入字符串流中
myStr.str("hello world");
myStr.str() // 返回string

//每次读取一个单词（以空格为界），存入str中
while(myStr >> str) {
    cout << str << endl;  
}
```

#### ostringstream
```cpp
vector<string> vec{"hello", "world"};
ostringstream mySteam;
for (auto num : vec) {
    mySteam << num;
}
// 输出helloworld
cout << mySteam.str() << endl;
```

#### stringstream
...
