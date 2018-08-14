---
title: TCO优化
date: 2018-04-27 17:10:54
tags:
---

这两天看下TCO做了下整理，参考的文章有:

1. [JS的递归与TCO尾调用优化](https://segmentfault.com/a/1190000004018047)
2. [Functional JavaScript – Tail Call Optimization and Trampolines](https://taylodl.wordpress.com/2013/06/07/functional-javascript-tail-call-optimization-and-trampolines/)

js里面之所以需要TCO的原因是因为，大多数浏览器没有TCO做优化，所以导致在运行递归时，会为每次递归开辟一个"栈"来存储数据。但是这种方式很快就会超出最大"栈"限制，导致"栈溢出"问题，<span style="color: red">`Uncaught RangeError: Maximum call stack size exceeded`</span>。

各个平台浏览器最大栈

|浏览器|macos|win|
|:---|:---:|:---|
|chrome|8967(15695)|11448|
|firefox|109152|18946|
|safari|28187||
|edge||16843|
|ie||6205|



比如上述文章中阶乘的例子:
``` javascript
function factorial(n) {
    if (n == 0) {
        return 1;
    } else {
        return n * factorial(n-1);
    }
}
```

在数量少的时候没啥问题`factorial(10)`， 但是一旦数量太大就会立马抛出溢出错误`factorial(1000000)`。
打开chrome性能分析工具可以看到，当第一次运行了函数`factorial`后会一直调用`factorial`。

![](/images/tco/performance.png)

第一步先改写方法:

``` javascript
function factorial(n) {
  function recur(n, acc) {
      if (n == 0) {
          return acc;
      } else {
          return recur(n-1, n*acc);
      }
  }
  return recur(n, 1);
}
```

## 优化方法

其实, 唯一的解法就是`复用栈`, 但是实现的方式可以是多种多样.

根据上面文章的提示，总结下大概有以下几种方法。

#### 1.改用循环
一种修改最大，但是最简单的就是把递归改循环。
``` javascript
function factorial(n) {
    let acc = 1;
    while(n) {
        acc *= n;
        n--;
    }
    return acc;
}
```
``` javascript
function factorial(n) {
    let acc = 1;
    for (let i=n; i > 0; i--) {
        acc *= i;
    }
    return acc;
}
```

基本上所有的递归函数都可以改成轮询，但是缺点就是可能因此代码的逻辑性变差。

#### 2.babel || 有TCO优化的引擎

在有TCO优化引擎或者babel中我们可以这么优化上诉的逻辑。
babel可以使用`babel-plugin-tailcall-optimization`插件进行转化

``` javascript
function factorial(n) {
  function recur(n, acc) {
      if (n == 0) {
          return acc;
      } else {
          return recur(n-1, n*acc);
      }
  }
  return recur(n, 1);
}
```

将递归放入到函数内部。

> 注意: 这个操作只能, 对单个递归函数进行分析, 如果是多个函数相互嵌套就无法使用。

#### 3.trampoline(蹦床) 函数

第三种方法就是利用trampoline 函数，相对循环方法，只需要修改一小部分的代码就可以完成同样的效果。

``` javascript
function trampoline(f) {
    while (f && f instanceof Function) {
        f = f();
    }
    return f;
}
```

将上述的`factorial`改成如下:

``` javascript
function factorial(n) {
    function recur(n, acc) {
        if (n == 0) {
            return acc;
        } else {
            return recur.bind(null, n-1, n*acc);
        }
    }
    return trampoline(recur.bind(null, n, 1));
}
```

只需要将`return`的函数加上`bind`，并在最外层返回是加上`trampoline`就算是修改完成。
简单说就是`trampoline`函数放入到循环中不停的接收、执行函数。

此外，第一篇文章还给出了trampoline一个种优化函数，修改的代码更少。
``` javascript
function tco(f) {
    const accumulated = [];
    let active = false;
    let value;

    return function accumulator(...args) {
      
        accumulated.push(args);

        if (!active) {
            active = true;

            while (accumulated.length) {
                value = f.apply(this, accumulated.shift());
            }

            active = false;

            return value;
        }
    }
}
```

递归代码修改成如下
``` javascript
let factorial = tco((n, acc) => {
    if (n == 0) {
        return acc;
    } else {
        return factorial(n - 1, n * acc);
    }
})

factorial(10, 1)
```
