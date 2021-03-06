---
layout:     post
title:     "js的一些基础知识"
subtitle:   "闭包，作用域链，原型链，继承"
date:    2016-03-09 09:25:17
author:   "Xsp"
header-img: "img/post-bg-default.jpg"
catalog:    true
tags:
    - 前端
---

### 作用域链
指向变量对象的指针列表，只引用但不实际包含变量对象。一般，函数执行完毕之后，局部活动对象就会被销毁，内存中仅保留全局作用域，但闭包却不同。

### 闭包
闭包是一个函数，它能使用函数定义外的变量，有权访问另一个函数作用域中的变量。使用全局变量就是一个简单的闭包的实例。创建闭包的常见方式就是在一个函数内部创建另外一个函数。
原理： 内部函数的作用域链包含外部函数的作用域链。外部函数执行完毕后其活动对象不会被销毁，因为内部匿名函数的作用域链仍然在引用这个活动对象。也就是说，当外部函数返回后，其执行环境作用域链被销毁，但活动对象仍保留在内存，直到内部匿名函数被销毁。

缺陷：闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包。

### 原型链
实现继承的主要方法，基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。
构造函数，原型，实例的关系：每一个构造函数都有一个原型对象，原型对象包含一个指向构造函数的指针，实例包含一个指向原型对象的指针。
实现：重写原型对象，用父对象的一个实例代替子对象的原型。
原型链的末端：null。所有函数的默认原型都是Object的实例。
确定原型和实例的关系：instanceof/isPrototypeOf
通过原型链继承的问题：
+ 包含引用类型的值，一个实例修改，其他实例也会被修改。
+ 创建子类实例时无法向超类型的构造函数传递参数。
构造函数继承： 则可以解决以上问题。但构造函数的问题是方法都在构造函数内部定义，无法函数复用。而且，超类中定义的方法在子类中是不可见的。
组合继承：通过原型链实现对原型属性和方法的继承，通过构造函数实现对实例属性的继承。但组合继承的超类型在使用过程中会被调用两次；一次是创建子类型的时候，另一次是在子类型构造函数的内部。
寄生组合继承：解决了两次调用的问题。

### 继承
+ 原型继承
```javascript
    function Super() {
        this.colors = ["red", "blue"];
    }

    function Sub() {
        return this.color;
    }
    Sub.prototype = new Super();
    //使用字面量添加方法，会覆盖上面的prototype,使继承无效
    getColor: function() {
        return this.color;
    }
```
+ 构造函数继承
```javascript
    function Super() {
        this.colors = ["red", "blue"];
    }

    function Sub() {
        Super.call(this);
    } 
```
+ 组合继承
```javascript
    function Super(name) {
        this.name = name;
        this.colors = ["red", "blue"];
    }
    Super.prototype.sayName = function() {
        alert(this.name);
    }

    function Sub(name) {
        Super.call(this, name);
    }

    Sub.prototype = new Super();

    var test = new Sub(111);
    test.sayName();
```
+ 原型式继承
```javascript
    function object(o) {
        function F() {};
        F.prototype = o;
        return new F();
    }
```
基于已有的对象创建新对象，同时还不必因此创建自定义类型。ES5的object.create()方法规范化了原型式继承，接受两个参数，一个作为新对象原型的对象，一个为新对象定义额外属性的对象，传入一个参数时与上述object行为相同。
+ 寄生式继承
```javascript
    function create(o) {
        var clone = object(o);
        clone.run = function() {
            alert("hello");
        };
        return clone;
    }
```
+ 寄生组合式继承
```javascript
    function Super(name) {
        this.name = name;
        this.colors = ["red", "blue"];
    }
    Super.prototype.sayName = function() {
        alert(this.name);
    }

    function Sub(name) {
        Super.call(this, name);
    }

    inheritPrototype(sub, super);

    function inheritPrototype(sub, super) {
        var prototype = object(super.prototype);
        prototype.constructor = sub;
        sub.prototype = prototype;
    }
```
