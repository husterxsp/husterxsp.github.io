---
layout: post
title: Python学习笔记
date: 2017-04-30
author: "Xsp"
catalog: true
tags:
    - Python
---

教程：[廖雪峰Python教程](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)

### Python基础
1. Python是解释型语言。编译和解释的区别？
    1. 编译型语言在编译过程中生成目标平台的指令，解释型语言在运行过程中才生成目标平台的指令。
    2. 虚拟机的任务是在运行过程中将中间代码翻译成目标平台的指令。
2. 输入/输出(Input/Output，IO)
    1. `print('hello，world’)`
    2. `input()`
3. 字符串
    1. 用`r''`表示`''`内部的字符串默认不转义
    2. 用`'''...'''`的格式表示多行内容
4. 布尔值
    1. `True / False`
    2. `and`、`or`、`not`
5. 特殊的空值 `None`
6. 除法运算
    1. `/` 除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数
    2. `//` 地板除，两个整数的除法仍然是整数
7. 字符编码
    1. ASCII，1个字节
    2. Unicode，2~4个字节
    3. UTF-8，可变长编码，1~6字节(此处有疑问，到底几个字节？)
    4. 计算机内存使用Unicode编码，硬盘或传输时，转换为UTF-8编码？
    参考：
        1. [知乎回答](https://www.zhihu.com/question/52346583)
        2. [字符编码笔记：ASCII，Unicode和UTF-8](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)
    5. 单字符转换：
        1. ord('A')，'A' => 65，one-charater => unicode code point.
        2. chr(65)， 65 => 'A'，to a charater

    6. 字符串转换为指定编码格式
        1. encode，str => bytes
        2. decode，bytes => str
        3. len()，str字符数 / bytes 字节数
8. 关于注释
    1. `#!/usr/bin/env python3`，Linux/OS X系统中表明是python程序，例如直接通过 `./xxx.py` 来运行就需要这一行注释
    2. `# -*- coding: utf-8 -*-`，告诉Python解释器，按照UTF-8编码读取源代码

9. 格式化输出
    1. 与C类似，用`%`实现，
        1. %d 整数
        2. %f 浮点数
        3. %s 字符串
        4. %x 16进制

10. list和tuple
    1. list 列表，有序集合，跟数组类似
    2. tuple 元组，不可更改的list，初始化后更安全

11. dict和set
    1. dict 字典，无序键值对，查找快但需要更多空间，类似map
    2. set

### 函数

1. `from fileA import funcB`, 从文件fileA导入函数funcB
2. `pass`, 占位符
3. 参数
    1. 默认参数会导致的问题, 不是很懂？
    2. `*parma` 可变参数, 接收tuple
    3. `**parma` 关键字参数， 接受dict
6. 递归
    1. 逻辑清晰，但存在栈溢出
    2. 对尾递归优化的语言，可以通过尾递归防止栈溢出
    3. 汉诺塔
        ```python
        #!/usr/bin/env python3
        # -*- coding: utf-8 -*-

        def move(n, a, b, c):
            if n == 1:
                print(a + '=>' + c)
            else:
                move(n - 1, a, c, b)
                move(1, a, b, c)
                move(n - 1, b, a, c)

        move(3, 'A', 'B', 'C')
        ```

### 高级特性

1. list切片
    ```python
    L=list()
    L:[i:j]
    ```
2. 迭代
    ```python
    for ... in
    for value in d.values()
    or k, v in d.items()

    #判断是否可迭代
    from collections import Iterable
    isinstance('abc', Iterable) # str是否可迭代
    ```
3. 列表生成式
    1. 生成list `list(range(1, 11))` => `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`
    2. `[x * x for x in range(1, 11)]` => `[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]`
4. 生成器generator, 一边循环一边计算, 相对于list更节省空间
    ```python
    g = (x * x for x in range(10))
    next(g)
    ```

### 函数式编程

> 我们通过把大段代码拆成函数，通过一层一层的函数调用，就可以把复杂任务分解成简单的任务，这种分解可以称之为面向过程的程序设计。
  由于函数内部的变量状态不确定，同样的输入，可能得到不同的输出，因此，这种函数是有副作用的。
  函数式编程的一个特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数！

1. 高阶函数，把函数作为参数传入
    1. map接收两个参数，一个是函数，一个是Iterable，map将传入的函数依次作用到序列的每个元素，并把结果作为新的Iterator返回。    
        ```python
        def f(x):
            return x * x

        r = map(f, [1,2,3])
        print(list(r))  # [1,4,9]
        ```
    2. reduce把一个函数作用在一个序列[x1, x2, x3, ...]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算
    3. filter()也接收一个函数和一个序列。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素
2. 匿名函数 `lambda x: x * x`等同于
    ```python
    def f(x)
        return x * x
    ```
3. 装饰器，python的@语法可以把decorator置于函数的定义处
    ```python
    def log(func):
        def wrapper(*args, **kw):
            print('call %s():' % func.__name__)
            return func(*args, **kw)
        return wrapper

    @log
    def now():
        print('2015-3-25')
    ```
4. 偏函数，固定函数的某些参数

### 模块
1. python 内置函数列表：https://docs.python.org/3/library/functions.html
2. 为避免模块名冲突，引入包，包目录下需定义`__init__.py`文件
3. 安装模块 `pip3 install xxx`, 模块搜索路径 `sys.path`

### 面向对象编程(Object Oriented Programming，OOP),
> 一种程序设计思想。OOP把对象作为程序的基本单元，一个对象包含了数据和操作数据的函数。

1. 类和实例
    ```python
    #!/usr/bin/env python3
    # -*- coding: utf-8 -*-

    class Student(object):
        def __init__(self, name, score):
            self.name = name
            self.score = score
        def print_score(self):
            print(self.name, self.score)

    bar = Student("hello", 2)
    bar.print_score()
    ```
2. 继承和多态
    1. 子类继承父类(基类、超类)
    2. 什么是多态？
3. 获取对象信息
    1. type() 判断对象类型
    2. isinstance() 判断class类型
    3. dir() 获取对象的所有属性和方法
    4. getattr()、setattr()、hasattr()，属性操作
    5. 鸭子类型？

### 面向对象高级编程
> 封装、继承、多态。

1. __slots__ (slots翻译，限制)限制class能添加的属性，对当前类实例起作用、对继承的子类不起作用，除非在继承的子类中也定义__slots__
2. @property，装饰器，把一个方法变为属性调用
3. 多重继承。MixIn可以给一个类增加多个功能
4. 定制类？__str__、__repr__、__iter__、__getitem__。。。
5. 使用枚举类。
    1. @unique装饰器可以帮助我们检查保证没有重复值。
6. 元类：
    > 动态语言和静态语言最大的不同，就是函数和类的定义，不是编译时定义的，而是运行时动态创建的。

    1. type()函数既可以返回对象类型，又可以创建出新的类型。

### IO编程

> 把变量从内存中变成可存储或传输的过程称之为序列化

### 进程和线程

任务 => 进程(Process)
子任务 => 线程(Thread)

多任务实现方式
+ 多进程
+ 多线程
+ 多进程 + 多线程

Python既支持多进程，又支持多线程
1. 进程
+ Unix/Linux操作系统提供了一个fork()系统调用，它非常特殊。普通的函数调用，调用一次，返回一次，但是fork()调用一次，返回两次，因为操作系统自动把当前进程（称为父进程）复制了一份（称为子进程），然后，分别在父进程和子进程内返回。
+ 子进程永远返回0，而父进程返回子进程的ID。这样做的理由是，一个父进程可以fork出很多子进程，所以，父进程要记下每个子进程的ID，而子进程只需要调用getppid()就可以拿到父进程的ID。
2. 线程
+ 多线程中，所有变量都由所有线程共享，所以，任何一个变量都可以被任何一个线程修改，因此，线程之间共享数据最大的危险在于多个线程同时改一个变量，把内容给改乱了。
