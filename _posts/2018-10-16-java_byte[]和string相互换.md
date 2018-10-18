---
layout: post
title: java byte[]和String相互换
date: 2018-10-16
author: "Xsp"
catalog: true
tags:
    - Java
---



```java
String example = "This is an example";

// ----------String to byte[]-----------------
// 默认都是UTF-8
// byte[] bytes = example.getBytes("UTF-8");
// 也能像下面这样写。StandardCharsets类里定义了一些静态常量指定编码格式。
// byte[] bytes = example.getBytes(StandardCharsets.UTF_8);
byte[] bytes = example.getBytes();

// [B@7ea987ac
System.out.println(bytes);

// ----------byte[] to String-----------------
// 字节数组转换为String的时候可以设置下编码，默认转换的时候是 UTF-8 编码
// String s = new String(bytes);
String s = new String(bytes, "UTF-8");

System.out.println(s);

// toString默认打印出来的只是 
// getClass().getName() + "@" + Integer.toHexString(hashCode());
// [B@7ea987ac
System.out.println(bytes.toString());

// [84, 104, 105, 115, 32, 105, 115, 32, 97, 110, 32, 101, 120, 97, 109, 112, 108, 101]
System.out.println(Arrays.toString(bytes));
```



Array.toString() 方法，其实就是一个个打印数组的元素

```java
// Arrays.java JDK1.8
public static String toString(byte[] a) {
    if (a == null)
        return "null";
    int iMax = a.length - 1;
    if (iMax == -1)
        return "[]";

    StringBuilder b = new StringBuilder();
    b.append('[');
    for (int i = 0; ; i++) {
        b.append(a[i]);
        if (i == iMax)
            return b.append(']').toString();
        b.append(", ");
    }
}
```





### byte[] 比较



```java
public static int compareByteArray(byte[] arr1, byte[] arr2) {
    int ret = 0;
    if (arr1.length != arr2.length) {
        ret = arr1.length - arr2.length;
    } else {
        for (int i = 0; i < arr1.length; i++) {
            if (arr1[i] != arr2[i]) {
                ret = arr1[i] - arr2[i];
                break;
            }
        }
    }
    return ret;
}
```



### 参考

- https://www.mkyong.com/java/how-do-convert-byte-array-to-string-in-java/
- https://www.journaldev.com/770/string-byte-array-java





