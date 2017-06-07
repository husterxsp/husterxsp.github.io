---
layout: post
title: C++位运算
date: 2017-06-07
author: "Xsp"
catalog: true
tags:
    - C++
---

### 设置bit
```cpp
int mask = 1 << x;
number |= mask;
```
确定想将某一位设置为1，只需要将mask的对应位设置为1，然后位或运算。所以，以上代码会设置number的右数第x位为1（x从0开始）。

### 清除bit
```cpp
number &= ~(1 << x);
```
确定想将某一位设置为0，需要mask的对应位置为0，其他位置为1，然后位与运算。那么可以先将mask对应位置为1，然后位取反。

### 切换bit
```cpp
number ^= 1 << x;
```
想toggle某一位的话，那就是位异或了，毕竟相同为0，不同为1。

### 检查bit
```cpp
bit = (number >> x) & 1;
```

### 设置和清除
```cpp
number ^= (-x ^ number) & (1 << n);
```
以上代码将第n位设置为x。

```
假设 number = 5（0000...0101）。n = 1，1 << n = 0000...0010
x = 0：
-x 为 1000...0000；
-x ^ number = 1000...0101；
(-x ^ number) & (1 << n) = 0000...0000

x = 1：
-x 为 1111...1111；
-x ^ number = 1111...1010；
(-x ^ number) & (1 << n) = 0000...0010

...
```
这个有点复杂啊。。。

### bitset标准库
```cpp
bitset<32> bitvec4("1100"); // bits 2 and 3 are 1, all others are 0
bitvec4.set(0)      // 将bit 0 置为1
bitvec4.reset(0)    // 复位bit 0
```

### 移位运算
左移（<<）：在右侧补0。

右移（>>）：如果是unsigned，左侧补0；否则补符号位或0（与环境相关）。
> 关于符号位如何处理没有明确的规定，所以强烈建议仅将位运算用于处理无符号类型 -- 《C++ primer th5》

### 其他
关于负数，反码是除符号位，其他位按位取反。补码是反码+1。

所有的位运算的计算都是用补码。

### 参考：
+ [How do you set, clear, and toggle a single bit - stackoverflow](https://stackoverflow.com/questions/47981/how-do-you-set-clear-and-toggle-a-single-bit)
