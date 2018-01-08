---
layout: post
title: Java 容器
date: 2018-01-07
author: "Xsp"
catalog: true
tags:
    - Java
---

《Thinking in Java》11章

### 数组
相同类型的，用一个标识符名称封装到一起的一个对象序列或者基本类型数据序列，可以通过方括号 `[]` 访问。\\
java的主要目标之一就是安全，所以不会有数组越界访问，并且数组都会被初始化。和其他容器的区别？
+ 数组需要设置大小，而其他容器类不需要。
+ 数组可以保存基本数据类型，其他容器类只能保存对象。

定义：
```java
int[] a; // 这种更合理，表明类型是 “一个int型数组”
int a[];
```

编译器不允许指定数组大小(因为Java在数组的声明中并不为数组元素分配内存)，现在拥有的只是对数组的一个引用。

初始化：
```java
// 基本类型数组
int[] a = {1,2,3,4,5};  // 方式一，只能在创建数组的地方使用。
int[] a1; a1 = a;       // a1 此时是对 a 的一个引用。
// 非基本类型数组，亦即引用数组
Integer[] a2;
// 方式二
Integer[] a = {        
    new Integer(1),
    new Integer(2),
};
// 方式三
Integer[] a = new Integer[]{        
    new Integer(1),
    new Integer(2),
};
```

一些基本成员：

```java
a.length            // 数组长度
Array.toString(a);  // 一维数组的可打印版本，属于java.util
```

二维数组：

### 泛型和类型安全的容器

注解
```java
// Java SE5 特有的注解，用来抑制警告信息。注解以@开头，可以接受参数。
// 此处表示只有 有关 "不接受检查的异常" 的警告信息应该被抑制。
@suppressWarning("unchecked");
```

ArrayList
```java
ArrayList.add();
ArrayList.get();
ArrayList.size();

// 定义时未指定类型作为泛型参数。（我理解为就是没指定类型？）
ArrayList apples = new ArrayList();
// 可以存入任何对象
apples.add(new Apple());
apples.add(new Orange());
// 取的时候需要进行类型转换，get得到的只是Object对象。
(Apple)apples.get(0);

// 指定类型参数，通过这种泛型，编译器会检查放置在容器中的对象类型，同时在取对象的时候get会自动进行类型转换，
// 因为List知道保存的是什么类型
ArrayList<Apple> apples = new ArrayList<Apple>();

// 输出Object.toString() 产生的字符串，打印类名 + 该对象的散列码
System.out.println(apples.get(0));
```

### 基本概念

容器类库的用途是保存对象，分为两个基本概念：
+ Collection。一个独立的元素序列。
+ Map。一组成对的"键值对"对象。ArrayList 从某种意义上也算是map,因为它将数字下标与对象关联了（虽然它是Collecton的一个子类）。
映射表，也称关联数组、字典。

```java
// ArrayList会被向上转型为List
List<Integer> a = new ArrayList<Integer>(); // （注意new 的时候只能写子类？）
// 这种使用接口的好处是，如果需要修改的话，只在创建的地方修改就可以。
List<Integer> a = new LinkedList<Integer>();
// 但是如果想使用如LinkedList中的一些特殊的方法时就不能这样向上转型。
```

### 添加一组元素


### 容器打印
数组的打印需要Array.toString(), 但是容器的打印其实不需要任何帮助

Collection
+ List，以特定的顺序保存一组元素
    + ArrayList
    + LinkedList
+ Set，元素不重复
    + HashSet，无序
    + TreeSet，有序
    + LinkedHashSet，插入的顺序
+ Queue，只允许在容器的一端插入元素，在容器的另一端删除元素。

Map
+ HashMap，无序
+ TreeMap，键的升序
+ LinkedHashMap，插入顺序

### List
+ ArrayList: 随机访问快（直接用下标），但是插入删除慢
+ LinkedList: 插入删除快（类比链表），但是随机访问慢

书上好像没说到Vector？


### 迭代器
可以用来遍历容器，而不必知道容器底层的结构。Java 迭代器只能单向移动。可以用来
+ iterator() 要求容器返回一个Iterator。
+ next() 返回当前元素，并且游标移一位
+ hasNext() 遍历是否结束
+ remove() 删除next()刚返回的元素

ArrayList 实现的代码：
```java
int cursor;       // index of next element to return
int lastRet = -1; // index of last element returned; -1 if no such]
int expectedModCount = modCount;
public boolean hasNext() {
    return cursor != size;
}
public E next() {
    checkForComodification();
    int i = cursor;
    if (i >= size)
        throw new NoSuchElementException();
    Object[] elementData = ArrayList.this.elementData;
    if (i >= elementData.length)
        throw new ConcurrentModificationException();
    cursor = i + 1;
    return (E) elementData[lastRet = i];
}
public void remove() {
    if (lastRet < 0)
        throw new IllegalStateException();
    checkForComodification();

    try {
        ArrayList.this.remove(lastRet);
        // 删除之前 cursor = i + 1; lastRet = i.
        // 删除后 cursor--;
        cursor = lastRet;
        // 删除一个元素后，lastRet重新赋值为-1，可见不能连续删除
        lastRet = -1;
        expectedModCount = modCount;
    } catch (IndexOutOfBoundsException ex) {
        throw new ConcurrentModificationException();
    }
}
```

#### ListIterator
继承Iterator，可以双向移动。但是只能用于List类的访问。
特有的方法
```java
ListItr(int index) {
    super();
    // 初始化时可以指定索引的位置
    cursor = index;
}

public boolean hasPrevious() {
    return cursor != 0;
}

public int nextIndex() {
    return cursor;
}

public int previousIndex() {
    return cursor - 1;
}

@SuppressWarnings("unchecked")
public E previous() {
    checkForComodification();
    int i = cursor - 1;
    if (i < 0)
        throw new NoSuchElementException();
    Object[] elementData = ArrayList.this.elementData;
    if (i >= elementData.length)
        throw new ConcurrentModificationException();
    cursor = i;
    return (E) elementData[lastRet = i];
}

public void set(E e) {
    if (lastRet < 0)
        throw new IllegalStateException();
    checkForComodification();

    try {
        ArrayList.this.set(lastRet, e);
    } catch (IndexOutOfBoundsException ex) {
        throw new ConcurrentModificationException();
    }
}
public void add(E e) {
    checkForComodification();

    try {
        int i = cursor;
        ArrayList.this.add(i, e);
        cursor = i + 1;
        lastRet = -1;
        expectedModCount = modCount;
    } catch (IndexOutOfBoundsException ex) {
        throw new ConcurrentModificationException();
    }
}
```

### LinkedList
和ArrayList一样实现了基本的List接口。但是ArrayList内部是用数组实现的。LinkedList内部使用对象Node实现的，通过对象的next和prev来链接元素。
```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```
另外LinkedList还添加了一些可以用作栈、队列或双端队列的方法。那还要栈、队列干嘛？

### 栈
使用LinkedList 实现的栈比 Stack 更好？

```java
class Stack<E> extends Vector<E> {}

public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {}

```

### Set

+ HashSet 使用散列
+ TreeSet 红黑树
+ LinkedHashSet 散列 + 链表

### Map
map的初始化？

```java
Map<Integer, Integer> m = new HashMap<Integer, Integer>();
m.put(1, 2);
m.put(2, 3);
System.out.println(m.containsKey(1));
System.out.println(m.containsValue(3));
```

### Queue
LinkedList 实现了Queue接口，可以向上转型为Queue。
```java
public class LinkedList<E> implements Deque<E> {}
public interface Deque<E> extends Queue<E> {}
// ...

Queue<Integer> q = new LinkedList<Integer>();

```

### PriorityQueue
优先队列算法通常在插入的时候进行排序（维护一个堆），也可能在移除的时候再进行选择。

### Collection 和 Iterator
Collection 是描述所有序列容器的共性的根接口。使用接口可以创建更加通用的代码。

### Foreach 与 迭代器
foreach可以用于任何 Collection 对象。因为java SE5引入了 `Iterable` 接口，该接口包含一个可以产生 `Iterator` 的 `iterator()` 方法，并且 `Iterable` 接口被用在foreach在序列中的移动。
```java
Collection<String> cs = new LinkedList<String>();
for (String c : cs) {
    System.out.print(c);
}
```

### 总结
1. Vector、HashTable、Stack已过时，不应该使用。
    + Vector、HashTable、Stack 是线程安全的。http://www.cnblogs.com/chenpi/p/5258450.html
    + 非线程安全是指多线程操作同一个对象可能会出现问题，可以使用synchronized关键字来同步控制以保证线程安全，
    但是会导致性能降低。http://blog.csdn.net/xiao__gui/article/details/8934832


Java 容器简图：

![](/img/post/2018-01-07-java-collection-1.jpg)


有时间再回看完善...