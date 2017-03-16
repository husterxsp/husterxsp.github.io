---
layout: post
title: "js for循环绑定问题解决"
date: 2015-06-27 13:08:14
author:  "Xsp"
header-img: "img/post-bg-default.jpg"
tags:
    - 前端
    - JavaScript
---

在网上看到的方法，第一个方法比较常见，第二种方法是第一次见，mark一下~
+ 方法一：立即执行函数（IIFE）
```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Document</title>
    </head>
    <body>
        <h1>0</h1>
        <h1>1</h1>
        <h1>2</h1>
        <h1>3</h1>
        <h1>4</h1>
        <script type="text/javascript">
            var head = document.getElementsByTagName("h1");
            for(var i=0;i<head.length;i++){
                (function(i){
                    head[i].onclick = function(){
                        alert(i);
                    };
                }(i));
            }
        </script>
    </body>
    </html>
```
+ 方法二：将 i 绑定到对应元素的的属性上
```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Document</title>
    </head>
    <body>
        <h1>0</h1>
        <h1>1</h1>
        <h1>2</h1>
        <h1>3</h1>
        <h1>4</h1>
        <script type="text/javascript">
            var head = document.getElementsByTagName("h1");
            for(var i=0;i<head.length;i++){
                head[i].index = i;
                head[i].onclick = function(){
                    alert(this.index);
                };
            }
        </script>
    </body>
    </html>
```
