---
layout: post
title: "相对路径"
date: 2015-06-22 13:16:30
author:  "Xsp"
header-img: "img/post-bg-default.jpg"
tags:
    - 前端
---


在代码中 ./代表当前路径 ../代表父级路径，相信大家都很清楚了，我想记录的是 “/”， 这个符号表示的是根目录，如果是在本地测试，比如：
`<img src="/images/hello.jpg">`
如果这个项目在G盘的话，那么它就会用G：/images/hello.jpg 去寻找资源，所以有时候就可能违背我们的原意了。
