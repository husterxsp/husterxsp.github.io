---
layout: post
title: join、CountDownLatch、CyclicBarrier
date: 2018-09-04
author: "Xsp"
catalog: true
tags:
    - Java
    - 多线程
---

## join、CountDownLatch、CyclicBarrier


### join

```java
public class Test {
    public static void main(String[] args) throws Exception {

        Thread thread = (new Thread(() -> {
            try {
                TimeUnit.SECONDS.sleep(5);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }));

        thread.start();

        thread.join();

        System.out.println("等待结束");
    }
}
```

如以上代码所示， 在main函数里调用 thread.join 后，main线程会在join调用出等待，等 thread 线程运行结束后再继续往后运行。

因为join 内部调用的是wait方法，所以调用join后，当前 main 线程会被挂起，对象上的锁会被释放（java编程思想 wait）



### CountDownLatch

```java
public class Test {
    static CountDownLatch c = new CountDownLatch(1);
    public static void main(String[] args) throws Exception {

        Thread thread = (new Thread(() -> {
            try {
                TimeUnit.SECONDS.sleep(5);
                c.countDown();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }));

        thread.start();
        c.await();
        System.out.println("等待结束");
    }
}
```

以上代码实现了相同的效果。CountDownLatch 与 join 功能相似，但是更强大。

CountDownLatch可以提供更加细粒度的等待。

join 需要等待对应的线程执行完成，而 CountDownLatch 对应的 await 只需要等待 count  减到 0 即可。例如不想等到某个线程完全结束，只想等待执行某一部分，则可以用CountDownLatch。

### CyclicBarrier

可循环使用（Cyclic）的屏障（Barrier）

```java
public class Test {
    static CyclicBarrier c = new CyclicBarrier(2);

    public static void main(String[] args) {
        new Thread(() -> {
            try {
                TimeUnit.SECONDS.sleep(1);
                c.await();
            } catch (Exception e) {

            }
            System.out.println(1);
        }).start();

        try {
            TimeUnit.SECONDS.sleep(5);
            c.await();
        } catch (Exception e) {
        }
        System.out.println(2);
    }
}
```

以上代码 创建的两个线程会在 await 方法调用处同步，即会等待，知道所有的线程都到达这个同步点时，再继续往下执行。

- CountDownLatch：一个或者多个线程，等待其他多个线程完成某件事情之后才能执行；
- CyclicBarrier：多个线程互相等待，直到到达同一个同步点，再继续一起执行。



### 参考

- 《java并发编程的艺术》
- https://www.jianshu.com/p/bce9f156080f

