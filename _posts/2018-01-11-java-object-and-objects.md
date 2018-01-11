---
layout: post
title: java Object和Objects区别
date: 2018-01-11
author: "Xsp"
catalog: true
tags:
    - Java
---
### java.lang.Object

java中所有的 class 都继承自 Object

### java.util.Objects

Objects 只是包含一些操作Object实例的工具方法，并且Objects 不能被实例化，它是final 的并且没有public的构造器。
只包含一些static 方法。

```java
public final class Objects {
    private Objects() {
        throw new AssertionError("No java.util.Objects instances for you!");
    }
    public static boolean equals(Object a, Object b) {
        return (a == b) || (a != null && a.equals(b));
    }
    //...
}
```

类似的一些用复数命名的class来存放一些工具方法命名方式在JDK中很常见。如
+ Collections
+ Arrays

参考：
[java.lang.Object VS java.util.Objects,what's the difference?
](https://stackoverflow.com/questions/13601209/java-lang-object-vs-java-util-objects-whats-the-difference)
