---
title: TypeScript 入门（4）——接口
date: 2016-04-29 17:28:01
tags:
---

接口是typescript里面的一种新的类型，它的主要作用就是为了类型检查为第三方代码定义契约

* ### 一个简单的接口
``` javascript
    interface LabelValue{
        label:string;
    }
    function test(label:LabelValue){
        console.log(label.label)
    }
    
    var obj = {label:"asdsa"};
    test(obj)
```
这里用interface 创建了一个接口，接口必须包含label键值，然后将test函数的参数的类型设置为LabelValue，那么test的label就必须是个包含label键值的对象，不然就会报错

* ### 可选属性
``` javascript
    interface Man{
        name:string;
        age?:number;
    }
    
    function createMan(attr:Man){
        console.log(attr.name);
        console.log(attr.age);
    }

    var obj1 = {name:"haha",age:123};
    var obj2 = {name:"haha"};
    createMan(obj1);
    createMan(obj2);
```
这个例子里面，我们把Man接口的age设置为了可选属性，为了测试我们在obj2少赋值了个age，而createMan(obj2)不会报错，这就是ts下option bages
* ### 函数类型
我们也可以为函数进行类型检测
``` javascript
    interface FnType{
        (source:string):boolean;
    }
    let myFn:FnType;
    myFn = function(source:string):boolean{
        console.log(source)    
        return !!source
    }
    myFn("test");
```
这里定义了类型一个函数接口，并把myFn的类型设置成FnType，myFn赋值一个匿名函数，函数的参数只能有一个并且类型是string返回值必须是boolean
* ### 数组类型
有了对象有了函数，我们再来定义一个数组类型的接口
``` javascript
    interface StringArr{
        [index:number]:string
    }

    var myArr = ["1","2","3"]
```
这里index意味着数组的索引被定义成了number，函数的类型为string
* ### 类类型接口
``` javascript
    interface Person{
        name:string
    }
