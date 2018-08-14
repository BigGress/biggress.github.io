---
title: Dom是否可见监听
date: 2018-04-11 21:43:38
tags:
---

在页面性能优化里面, 现在普遍开始提倡, 当页面滚动到某个图片元素时开始加载那个图片。所以, 今天来探讨下有几种实现方式。

假设我们有这样子的dom
``` html
<style>
  body, html {
    margin: 0;
    height: 0;
  }
  img {
    display: block;
    height: 100vh;
  }
</style>

<div class="box">
  <img class="content-1" data-src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1523470404210&di=9e0756eff787371b297db5f8741d4b32&imgtype=0&src=http%3A%2F%2Fimgsrc.baidu.com%2Fimgad%2Fpic%2Fitem%2F8b82b9014a90f6037cb445933312b31bb151edda.jpg" />
  <img class="content-2" data-src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1523470404210&di=e8b9b2b30df3fe6d856f4c3e19a6d420&imgtype=0&src=http%3A%2F%2Fpic15.nipic.com%2F20110803%2F7180732_211822337168_2.jpg"/>
  <img class="content-3" data-src="https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1523470462666&di=97f0de7b723fd2d40e6bde945d6073c0&imgtype=0&src=http%3A%2F%2Fimgsrc.baidu.com%2Fimage%2Fc0%253Dpixel_huitu%252C0%252C0%252C294%252C40%2Fsign%3Dc690ddf3edcd7b89fd6132c3665c27cb%2F8b82b9014a90f603611a417e3212b31bb051ed65.jpg"/>
</div>
```

1. IntersectionObserver API

IntersectionObserver 是一个可以监听DOM是否在视口的API。官方给出的解释如下:

> Intersection observer 允许你配置一个回调函数，每当target,元素和设备视口或者其他指定元素发生交集的时候该回调函数将会被执行。设备视口或者其他元素我们称它为root element 或者 root。特别是你想要监听(target)元素相对于浏览器视口(root)的交集变化的时候(intersection API初始化的时候 如果设置root参数为null或者不设置则root默认为设备视口)。无论你是使用viewport或者其他element做为root， intersection API都会以相同方式工作，每当tartget element在root 中的可见性交集数量发生变化(target元素的位置在root中发生变化)都会执行你提供的回调函数

总结下, 就是当你滚动到root元素使监听的元素可见时, 就会触发回调函数。

所以代码就如下。

``` javascript
let observer = new IntersectionObserver((images) => {
  console.log(`我是回调函数`, images);
  images.forEach(image => {
    if (image.intersectionRatio > 0) {
      let {target} = image;
      target.src = target.dataset.src;
    }
  })
})
Array.from(document.querySelectorAll("img")).forEach((e) => observer.observe(e))
```

执行后, 控制台立马就会输出`[IntersectionObserverEntry]`对象数组, 以判断dom是不是在视口中。IntersectionObserver会分别会在dom离开和进入视口时执行回调, 所以我们必须利用`intersectionRatio`来判断dom是否在视口中。`intersectionRatio`表示目标元素与视口的交叉区域的百分比。

2. 用scroll事件来监听

scroll 事件我就不多做介绍了。用scroll的原理就是利用getBoundingClientRect获取dom在页面中位置, 然后判断跟边界的上下左右距离是否在视口中。

``` javascript
let images = document.querySelectorAll("img");
document.addEventListener("scroll", () => {
  showImage();
})

showImage();

function showImage() {
  images.forEach(e => {
    if (isInView(e, document.documentElement)) {
      e.src = e.dataset.src
    }
  })
}

// 通过元素到边框的距离与页面滚动的高度来判断是否已经显示了元素
function isInView(el, root) {
  let bounding = el.getBoundingClientRect();
  return (bounding.top - (root.clientHeight + root.scrollTop)) < 0 &&
         (bounding.left - (root.clientWidth + root.scrollLeft)) < 0
}
```

接着做点节流的优化

``` javascript
let images = document.querySelectorAll("img");
let scrollFn = (() => {
  let timeout = null;
  return (time = 50) => {
    if (timeout) {
      clearTimeout(timeout);
    }

    timeout = setTimeout(() => {
      showImage();
    }, time);
  }
})()

document.addEventListener("scroll", scrollFn)

showImage();

function showImage() {
  images.forEach(e => {
    if (isInView(e, document.documentElement)) {
      e.src = e.dataset.src
    }
  })
}

// 通过元素到边框的距离与页面滚动的高度来判断是否已经显示了元素
function isInView(el, root) {
  let bounding = el.getBoundingClientRect();
  return (bounding.top - (root.clientHeight + root.scrollTop)) < 0 &&
         (bounding.left - (root.clientWidth + root.scrollLeft)) < 0
}
```

这就是我今天总结的2种显示滚动加载图片的方式。如果你需要配合framework使用可以将scroll的函数封装成一个服务, 这样子就只需要一个监听函数。
