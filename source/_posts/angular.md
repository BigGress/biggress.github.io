---
title: Angular 1.x 路由与fis3 结合
date: 2016-01-02 20:20:59
---

前一段时间做了一个angular的项目，这个项目中使用了angular的路由机制，所以今天做一个总结，算是正式结束这个项目。

## 加载方式

接触过angular路由的人应该知道，angular的路由有两种加载模板方式:一种是内存加载，一种是ajax加载。
内存加载的最大好处，可以把模板直接写入内存，当需要时直接加载，速度快，且与服务器通信比较少。但是内存加载的两种写法(srcript和js)的方式对于html的模块化和后期的维护上都产生了很大的麻烦。而Ajax加载更像是内存加载的一种补充，因为促发ajax加载的条件就是在内存中找不到相应的模板，这种方式也就会增加与服务器的通信，但就算如此ajax加载对于html的模块化和管理都还是很有帮助的。那在这样两难的情况下没有什么好的方法既可以保证加载的速度又可以方便html的模块化和后期的维护？

## fis的内容嵌入

那么敢写这篇博客肯定是有方法的，那就是fis3的内容嵌入,fis3的内容嵌入既可以在html中嵌入也可以在js中嵌入,我们今天主要说的是js的嵌入,
对fis3有兴趣的朋友可以访问http://fis.baidu.com 详细的学习。

那么应该怎么实现呢？

1.首先创建一份myrouter.html文件。
``` bash
<div>
    <p>我是html文件</p>
</div>
```

2.然后再router.js文件中用__inline引入刚刚的html。
``` bash
var router = angular.module('router',[]);

router.controller('first',['$tempalteCache',function($templateCache){
    $templateCache.put('myrouter.html',__inline('../view/myrouter.html'))
}])
```
3.在cmd中把刚刚写的js编译发布：
``` bash
fis3 release -d <发布的路径>
//这里也可以不写-d 和发布路径...不写了话会自动默认发布到c:\www这个路径下面
```
4.当通过fis编译发布出去后，发布的文件就会变下面这个样子：
``` bash
var router = angular.module('router',[]);

router.controller('first',['$tempalteCache',function($templateCache){
    $templateCache.put('myrouter.html',"<div>\r\n    <p>我是router文件</p>\r\n</div>")
}])
```


myrouter.html中内容就自动变成字符串引入到这份router文件中。

#### 建议

最后，建议大家可以吧，给templateCache起名字的时候，可以直接用路径起名。这样做什么好出呢？
因为angular的路由的机制是从内存先读是否有模板没有再通过ajax加载，所以假如服务器出现了问题导致这份router文件产生了问题，那么还是可以通过ajax请求到页面，不失为一种保险手段吧。

#### 信息
那么今天就分享到这里，由于是个新人如有不妥之处，欢迎与我联系QQ：451148156，我尽快改正。
