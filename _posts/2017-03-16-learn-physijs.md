---
layout: post
title: 学习Threejs物理引擎Physijs
date: 2017-03-16
author: "Xsp"
header-img: "img/post-bg-default.jpg"
catalog: true
tags:
    - Threejs
---

Physijs是Threejs物理引擎, 在Threejs之上又做了一层封装，可以提供一些如碰撞、掉落等物理效果，基于另外一个物理引擎[ammojs](https://github.com/kripken/ammo.js/), 通过Web worker另开一个线程来进行物理效果模拟的计算，避免阻塞渲染。官方文档 [wiki](https://github.com/chandlerprall/Physijs/wiki)。

制作赛车游戏中遇到的一些问题。

先放上 [demo](https://husterxsp.github.io/IFE-WebGL/task8/) 和 [代码](https://github.com/husterxsp/IFE-WebGL/tree/gh-pages/task8)

### 不会掉落的地面

Physijs加载场景之后，加载的物体都有默认的向Y轴负方向大小为10的加速度，可以通过```setGravity``` 设置。通过[设置mass 为 0 可以添加一个静止的地面](https://github.com/chandlerprall/Physijs/wiki/Freezing-an-object)。这里mass可以理解为物体的质量，为0物体静止不动，默认值为1，设置为一个[很小的小数如0.01则表示一个很轻的物体](https://github.com/chandlerprall/Physijs/issues/187)，比如被撞一下就可以撞飞了。
```JavaSctipt
    // 静止的地面
    var ground = new Physijs.BoxMesh(
        new THREE.BoxGeometry(128, 1, groundLen),
        groundMaterial,
        0 // mass
    );
```
注意这里用的BoxMesh来定义的地面，如果使用PlaneMesh来定义的话就不行，[wiki](https://github.com/chandlerprall/Physijs/wiki/Basic-Shapes)里说PlaneMesh是一个厚度无限接近0的平面，所以用这个的话，物体在掉落至接触这个平面时可能[速度太快无法检测到碰撞](https://github.com/chandlerprall/Physijs/issues/102)，就直接穿过地面掉下去了。

### 改变物体的位置

通过 Physijs 创建的物体，不能直接修改position和rotation等值，如需修改需要设置 ```mesh.__dirtyPosition = true;```。

### 穿过可得分物体

如果用Three来创建物体的话是可以直接穿过的，但是这里还需要用Physijs来处理一些碰撞检测，参考 [Ghost Objects](https://github.com/chandlerprall/Physijs/issues/82) 和 [Ball go across ground](https://github.com/chandlerprall/Physijs/issues/102) 的解释，可以设置
``` mesh._physijs.collision_flags = 4; ``` 来添加一个既可以穿过又可以检测到碰撞的物体。但是只添加这段代码的话，物体也会直接穿过地面掉下去的。还需要设置
```JavaScript
   shape.addEventListener('ready', function () {
        // 不是很懂。。
        // https://github.com/chandlerprall/Physijs/wiki/Collisions
        this.setCcdMotionThreshold(0.1);
        this.setCcdSweptSphereRadius(1);
    
        // 设置线速度和角速度为 0
        this.setLinearFactor(new THREE.Vector3(0, 0, 0));
        this.setAngularFactor(new THREE.Vector3(0, 0, 0));
    });
```
需要注意那段代码需要放在ready回调函数里面即物体加载完毕。

### 光照跟随物体移动

Physijs 的[小车例子](https://github.com/chandlerprall/Physijs/blob/master/examples/vehicle.html#L257)里给出的代码是这样的：

```JavaScript
    light.target.position.copy( vehicle.mesh.position );
```

但是这个比较奇怪，小车行驶一段距离后，不知道啥原因光照就没了。。。看了下[Three的文档](https://threejs.org/docs/index.html?q=lig#Reference/Lights/DirectionalLight)是直接设置target就好.

```JavaScript
    light.target = vehicle.mesh;
```

还有就是会出现奇怪的情况，小车有阴影，但是掉落的其他物体没有阴影，因为light.target设置为小车了，但是光线的范围太小照不到其他物体，
可以把光照的范围设置的大一些。Threejs里，平行光DirectionalLight用的是正投影相机OrthographicCamera。

```JavaScript
    light.shadow.camera.left = -80;
    light.shadow.camera.right = 80;
    light.shadow.camera.top = 50;
    light.shadow.camera.bottom = -50;
```

以上几个属性的[默认值都是5或-5](https://threejs.org/docs/index.html?q=light#Reference/Lights.Shadows/DirectionalLightShadow)
