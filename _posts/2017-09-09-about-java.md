---
layout: post
title: Java概述
date: 2017-09-09
author: "Xsp"
catalog: true
tags:
    - Java
---

《Java 核心技术 卷1》10th 学习笔记

Java是强类型、静态类型语言。 [弱类型、强类型、动态类型、静态类型语言的区别是什么？](https://www.zhihu.com/question/19918532/answer/21645395)

### 第二章 环境

#### 一些缩写
JDK(Java Development Kit, Java开发工具包): 编写Java程序的程序员使用的软件。

JRE(Java Runtime Environment, Java运行时环境): 运行Java程序的用户所使用的软件，包含虚拟机但不包含编译器，为不需要编译器的用户提供。

Java SE: Java 标准版

#### 运行
```
编译
javac xxx.java
执行
java xxx
```

javac是Java编译器，将xxx.java文件编译成xxx.class文件，java程序启动Java虚拟机，虚拟机再执行class文件中的字节码。

### 第三章 语法

数据类型：
1. 整型，int/short/long/byte
2. 浮点型，float/double
3. char型
4. boolean型

C/C++ 区分变量的声明与定义，在Java中不区分变量的声明与定义。

Java用final来指示常量

Java String 类型不可修改，只能重新赋值。


C/C++区分变量的声明和定义，但是Java不区分

### 第四章 对象与类
类之间的关系：
1. 依赖 uses-a
2. 聚合 has-a
3. 继承 is-a
