---
layout:     post
title: "关于angular的一些基础知识"
date:    2016-03-08 21:37:42
author:   "Xsp"
header-img: "img/post-bg-default.jpg"
catalog:   true
tags:
    - Angular
    - 前端
---

### 双向数据绑定
Angular使用的是脏检测，当改变数据之后，需要做一些事情来触发脏检测，然后再应用到这个数据对应的DOM元素上，可以使用$digest和$apply这两个函数来将数据更新。
$digest和$apply的差别：

+ $apply可以带参数，它可以接受一个函数，然后在应用数据之后，调用这个函数。
+ 当调用$digest的时候，只触发当前作用域和它的子作用域上的监控，但是当调用$apply的时候，会触发作用域树上的所有监控。因此，从性能上讲，如果能确定自己作的这个数据变更所造成的影响范围，应当尽量调用$digest，只有当无法精确知道数据变更造成的影响范围时，才去用$apply，很暴力地遍历整个作用域树，调用其中所有的监控。从另外一个角度，我们也可以看到，为什么调用外部框架的时候，是推荐放在$apply中，因为只有这个地方才是对所有数据变更都应用的地方，如果用$digest，有可能临时丢失数据变更。

Angular的数据更新是批量的，事件出发后进入digest 循环

### scope
```javascript
// AngularJS v1.3.0-beta.14 /12000行
function $RootScopeProvider() {
  function Scope() {}
  Scope.prototype = {
    constructor: Scope,
    $new: function() {};
    $watch: function() {};
    $watchGroup: function() {};
    $watchCollection: function() {};
    $digest: function() {};
    $destroy: function() {}
    $eval: function() {},
    $evalAsync: function() {};
    $$postDigest: function() {};
    $apply: function(expr) {}
    $on: function() {}
    $emit: function() {};
    $broadcast: function() {};
  }
  var $rootScope = new Scope();

  return $rootScope;
}
```

$rootScope： 根作用域

### 关于Controller

+ 不要在controller里操作DOM,很耗时，应该交给指令
+ 主要用于初始化时的数据绑定。

### service
service的功能是抽取contoller里的公共逻辑

+ 单例, 整个应用只有一个实例
+ 由$injector负责实例化
+ 在整个应用的生命周期存在，可以用来共享数据
+ 使用时依赖注入
+ 自定义service需写在内置的后面
+ 自定义service避免$开头

### factory,service,provider的区别：

```javascript
function provider(name, provider_) {
  assertNotHasOwnProperty(name, 'service');
  if (isFunction(provider_) || isArray(provider_)) {
    provider_ = providerInjector.instantiate(provider_);
  }
  if (!provider_.$get) {
    throw $injectorMinErr('pget', "Provider '{0}' must define $get factory method.", name);
  }
  return providerCache[name + providerSuffix] = provider_;
}

function factory(name, factoryFn) { return provider(name, { $get: factoryFn }); }

function service(name, constructor) {
  return factory(name, ['$injector', function($injector) {
    return $injector.instantiate(constructor);
  }]);
}
```

最终都是调用provider，只是写法不同

### 关于directive
+ 实现view的复用
+ 指令的四种方式 元素/属性/类/注释(E/A/C/M)，默认方式为 属性
+ 执行过程：
	+ 加载：页面加载angular,找到ng-app,确定应用边界
	+ 编译：遍历DOM，根据指令的template, replace, transclue 等转换DOM结构，若存在compile则调用
	+ 链接：对每条指令执行link函数，link函数一般用来操作DOM，绑定事件监听器
+ link用于处理指令内部事件，controller用于暴露一些方法给外部调用。
+ 指令独立作用域，避免指令间的数据相互影响。scope的绑定策略：
	+ @ 把当前属性作为字符串传递
	+ = 与父scope中的属性双向绑定
	+ & 传递一个来自父scope的指令，稍后调用
+ compile和link
	+ compile函数对指令模板进行转换
	+ link的作用是在模型和视图之间建立关联，包括在元素上注册事件监听器
	+ scope在链接阶段才会被绑定到元素，compile阶段操作scope会报错
	+ 对于同一个指令的多个实例，compile只会执行一次，而link对于每个实例都会执行一次
	+ 一般写link就够了
	+ 如果自定义compile函数，那么自定义的link函数将无效，因为compile函数应该返回一个link函数供后续处理。

### 启动过程

```javascript
(function(window, document, undefined) {
  'use strict';
  //....

  if (window.angular.bootstrap) {
    //AngularJS is already loaded, so we can return here...
    console.log('WARNING: Tried to load angular more than once.');
    return;
  }

  //try to bind to jquery now so that one can write angular.element().read()
  //but we will rebind on bootstrap again.
  bindJQuery();

  publishExternalAPI(angular);

  jqLite(document).ready(function() {
    angularInit(document, bootstrap);
  });

})(window, document);
```

+ 自执行函数，加载完后就调用。
+ window.angular.bootstrap检测是否多次导入angular
+ ng-app自动启动
+ 手动调用bootstrap启动
+ 页面中有多个ng-app的话，angular 只会启动第一个，第二个需要手动启动。
+ 查找编译指令
。。。

### 事件
+ $emit向上传播事件
+ $broadcast向下传播事件

### 性能优化
减少watcher的数目

### 关于promise
+ 链式的写法，避免长长的回调

### 用angular开发移动应用
+ Native App原生 / WebApp用phonegap，Appcan等工具打包前端代码 / Hybrid App(web view)
+ WebApp主要缺陷运行效率比较低。

写[醉晚](https://github.com/husterxsp/zuiwan-frontend "https://github.com/husterxsp/zuiwan-frontend")的时候尝试用过ionic，但也有很多坑，比如，IE下tab不居中问题，android下tab默认上方并且没有动画效果等等。后来弃了。

### 关于MVC

+ 切分职责和逻辑，便于维护。MVC只是手段，但目标是模块化和复用。

### 其他

ng支持一些基本的数学/比较/布尔/位/对象/数组等运算，但不支持if/for/while等控制逻辑;

+ 由于$digest机制和“对象深比较”机制，ng在处理Tree型结构方面性能很差，所以在处理一些层次比较深的数据结构时最好不要用双向绑定。
+ Model绑定到多个视图(观察者模式)
+ 如何知道Model发生变化(赃值检测$watch与$digest)
+ 如果Model深层嵌套，如何知道某个属性是否发生变化（对象深比较）
+ A,B两个方法相互watch对方，如何避免“震荡”(死锁)？（TTL机制，ng内部如果检测到10次变化就会停止）
+ 绑定如何支持内嵌表达式（$parser与$eval）


参考资料：

+ [http://www.imooc.com/learn/156](http://www.imooc.com/learn/156 "http://www.imooc.com/learn/156")
+ [https://github.com/xufei/blog](https://github.com/xufei/blog "https://github.com/xufei/blog")
