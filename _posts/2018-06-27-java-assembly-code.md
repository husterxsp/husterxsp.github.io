---
layout: post
title: Mac 查看java编译后的汇编代码
date: 2018-06-27
author: "Xsp"
catalog: true
tags:
    - Java
---



测试以下代码

```java
// Test.java
public class Test {
    volatile int sum = 0;
    public static void main(String[] args) {

    }
}
```

命令行参数的说明：[HotSpot/PrintAssembly](https://wiki.openjdk.java.net/display/HotSpot/PrintAssembly)
PrintAssembly 就是 “打印汇编”，开启 PrintAssembly 需要预先 开启 UnlockDiagnosticVMOptions。

```
$ javac Test.java && java -XX:+UnlockDiagnosticVMOptions -XX:+PrintAssembly Test
```
直接运行会报错
```
$ Could not load hsdis-amd64.dylib; library not loadable; PrintAssembly is disabled
```
需要下载 [hsdis-amd64.dylib](https://github.com/evolvedmicrobe/benchmarks/blob/master/hsdis-amd64.dylib)
然后移动到 jre lib 目录。

```
$ sudo mv hsdis-amd64.dylib /Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home/jre/lib
```

然后再运行就可以。

### 参考

- [https://blog.csdn.net/u012807459/article/details/52327603](https://blog.csdn.net/u012807459/article/details/52327603)
- [https://nitschinger.at/Printing-JVM-generated-Assembler-on-Mac-OS-X/](https://nitschinger.at/Printing-JVM-generated-Assembler-on-Mac-OS-X/)
