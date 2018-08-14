---
title: 利用ae动画嵌入web
date: 2018-03-29 12:18:03
tags:
---

最近跟群友讨论下怎么在Web上实现酷炫动画。比如
![](/images/ae-1.gif)

![](/images/ae-2.gif)

我之前能想到的方法就是直接利用video标签嵌入页面、或者直接用D3这些第三方库手写动画。但是朋友告诉我利用bodymovin.js将ae的动画倒入到web中, 看到这个所以打算来试试AE。

于是我做了一个非常简单的ae动画, 将一个块从左边移动右边([效果](/images/ae-3.mov))。然后在[lottie-web](https://github.com/airbnb/lottie-web/)中找到ae的bodymovin插件并安装, 安装自然可以再插件中进行渲染动画。输入的文件会是一个[json](/images/ae-4.json)文件。

在效果有效果文件, 新建一个web, 然后引入bodymovin.js, 并且利用bodymovin对象导入动画。
``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
        <div id="animation"></div>
    <script src="https://cdn.bootcss.com/bodymovin/4.13.0/bodymovin.min.js"></script>
    <script>
    bodymovin.loadAnimation({
            path:'data.json',   //json文件路径
            loop:true,
            autoplay:true,
            renderer:'canvas',  //渲染方式，有"html"、"canvas"和"svg"三种
            container:document.getElementById('animation')
        });
        </script>
</body>
</html>
```
最终播放效果。
![](/images/ae-5.gif)
