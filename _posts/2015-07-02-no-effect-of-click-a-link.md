---
layout: post
title: "给a标签添加点击事件却无效"
date: 2015-07-02 12:57:54
author:  "Xsp"
header-img: "img/post-bg-default.jpg"
tags:
    - 前端
    - JavaScript
---

整理笔记，看到一个bug,代码大意如下:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style type="text/css">
        div {
            width: 100px;
            height: 20px;
            background-color: red;
            display: none;
        }
    </style>
</head>
<body>
<div></div>
<a href="">点击</a>
<script>
    document.querySelector('a').onclick = function(){
        document.querySelector('div').style.display = 'block';
    }
</script>
</body>
</html>
```

运行后会发现div并没有添加样式，但是会看到一瞬间的闪现，打断点调试可以观察到，点击事件之后，页面又刷新了。
原因：a标签的href会导致默认人的页面跳转事件，如果为空值，就会刷新页面，所以就会出现上述的问题。可以改为href='#'或者href="javascript:;"等。或者使用e.preventDefault();解决。

另附上一个回答：

 - [html页面a标签可以不写href属性吗？](http://segmentfault.com/q/1010000002699099)
