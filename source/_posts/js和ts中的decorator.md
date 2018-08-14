---
title: js和ts中的decorator
date: 2018-05-06 19:20:26
tags:
---

前一段时间看了下，js中的装饰器，感觉未来这项语法会有许多的前景。所以这里来说说2个方面:
1. 怎么在js/ts中使用装饰器。
2. 装饰器的常见用法(发射日志、类型验证、类添加属性等)。

在开始前, 有一点值得说明下, 装饰器的生成都是编译的时候, 而不是运行的时候, 所以`this`这些可能对装饰器无法使用了。

#### js中使用装饰器
如果想在js中使用装饰器就必须使用babel, 需要添加"transform-decorators-legacy"插件, 配置如下:

项目中`npm i babel-plugin-transform-decorators-legacy`。

.babelrc配置修改如下.
```
  plugins: ["transform-decorators-legacy"] 
```

#### ts中使用装饰器
在ts中使用装饰器就比较简单, 只要在`tsconfig.json`中添加一个配置就可以了:

`tsconfig.json`
``` json
{
  "experimentalDecorators": true
}
```

#### 常见用法

现在装饰器一般一共有四种用法, js只支撑2种, ts都支撑, 他们分别是:

1. 对`类`的修饰(js、ts)
2. 对`方法`的修饰(js、ts)
3. 对`属性`访问的修饰(ts)
4. 对`参数`的修饰(ts)

下面分别说说有哪些常见的用法。
对`类`修饰中, 可以给`类`额外添加一些属性或者方法, 比如在[angular](https://angular.io/guide/displaying-data)中就有这样子的用法, 有兴趣的朋友可以去看看angular使用方法。

在对`类`的装饰器中, 我们可以给类加一些属性或者方法。

``` javascript
function Component(obj) {
  return function(target) {
    target.type = "It's a component";
  }
}

function addRender(target) {
  target.prototype.render = function() {
    console.log("i'm render");
  }
}

@addRender
@Component({
  select: "test"
})
class TestCom {
  
}
```

在对`方法`的装饰器中, 我们修改方法的, 让它执行之前或者之后触发一些操作。

// 比如我们有一个loading函数, 那么我们就可以这么使用
``` javascript
function loading(target, keyname, props) {
  let oldFn = props.value;
  props.value = function(...args) {
    console.log("start loading");
    return oldFn.apply(this, args).then(() => {
      // loadService.endLoading();
      console.log("end loading");
    });
  }
}

class MyClass {

  @loading
  doAsyncAction() {
    return new Promise((res) => setTimeout(res, 1000))
  }
}
```

在对`属性`访问的装饰器中, 属性访问这一层, js可以直接使用`Object.defineProperty`来设置, ts中允许直接设置属性, 所以这里我就写谢ts的用法。

``` typescript
function readPrototype(target, keyname, props) {
  // console.log(target, keyname, props);
  console.log(`read: `, keyname);
}

class AB {
  @readPrototype get a() {
    return "a";
  }
  constructor() {}
}

let b = new AB();

console.log(b.a);
```

在对`参数`中, 我们可以借助装饰器进行些类型的验证。

``` typescript
function isString(target, keyname,index) {
  // console.log(args);
  target[`__type_check__`] = [`${index}:string`];
  return target;
}

function checker(target, keyname, props) {
  console.log(target, keyname, props)
  let oldValue = props.value;
  props.value = function(...args) {
    if (target[`__type_check__`] && target[`__type_check__`].length) {
      target[`__type_check__`].forEach(e => {
        let [i, type] = e.split(":");
        if (args[i] !== type) {
          throw new Error(`${keyname} must ${type}`)
        }
      })
    }
    let firstItem = args[0];
    oldValue.apply(this, args)
  }
}

class C {
  constructor() {}

  @checker
  abc(@isString txt) {
    console.log(`abc`)
  }
}

let ha = new C();

ha.abc(123);
```