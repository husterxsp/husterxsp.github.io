---
layout: post
title: Java 数组
date: 2018-01-10
author: "Xsp"
catalog: true
tags:
    - Java
---

《Think in Java》 16章

### 数组的特殊性

+ 效率。在Java中数组是效率最高的存储和随机访问对象引用序列的方式。代价是大小被固定。
+ 类型。在泛型之前，其他类型的容器在处理对象时，都视为没有任何具体类型，即当做Object。而数组可以指定持有某种具体类型，
可以在编译的时候就可以检查是否插入了不正确的类型。
+ 保存基本类型。泛型之前的容器不能保持基本类型，但有了泛型，容器可以指定并检查所持有的对象的类型，
并通过自动包装机制持有基本类型。

数组用 `[]` 访问元素，ArrayList 用get() 和add() 等访问。

有了自动包装机制后，数组仅存的优点就是 效率。

### 数组是第一级对象
数组标识符只是一个引用，指向堆中创建的一个真实的对象。

### 返回数组
C/C++ 不能返回数组，只能返回指向数组的指针。而Java可以直接返回一个数组。


### 多维数组
数组的每一维的长度不固定，也被称为粗糙数组。

```java
int[][] a = {
    {1, 2, 3},
    {2, 3}
};

System.out.println(Arrays.deepToString(a));

// autoboxing (自动包装机制)
Integer[][] a = {
    {1,2,3}
};
```

### 数组与泛型
数组与泛型不能很好地结合，不能实例化具有参数化类型的数组
```java
// error

```

 待再看。。。

### 创建测试数据

`Array.fill()` 用同一个值填充数组

关于数据生成器待看。。。

### Arrays 实用功能

+ equals(), 判断数组是否相等。deepEquals() 用于多维数组
+ fill(), 填充数据
+ sort(), 排序
+ binarySearch(), 在已排序的数组中查找
+ toString()
+ hashCode(), 产生数组的散列码

#### 复制数组

arraycopy 可以复制基本类型或者对象，但是对象的话是只复制引用，是浅复制。
另外arraycopy 不会进行自动包装和自动拆包，所以复制的两个数组必须要具有相同的数据类型。

```java
public static native void arraycopy(Object src,  int  srcPos,
                                    Object dest, int destPos,
                                    int length);
```

为什么说 arraycopy 比for循环复制的速度快？一个原因可能是 arraycopy 复制的是整块内存，而不是单个的数组元素。
[https://stackoverflow.com/questions/18638743/is-it-better-to-use-system-arraycopy-than-a-for-loop-for-copying-arrays](https://stackoverflow.com/questions/18638743/is-it-better-to-use-system-arraycopy-than-a-for-loop-for-copying-arrays)

但是好像还是没说清楚，似乎是这个arraycopy的效率还要视具体情况而定，如链接里面说的

在数组size小的时候，差别不大。但size 比较大的时候差别比较明显。

另外String 数组 arraycopy比较慢，但是byte数组 arraycopy 比较快。



另外arraycopy 是native 方法，这意味着它是由非java语言实现的，如C，所以性能会好一些。
