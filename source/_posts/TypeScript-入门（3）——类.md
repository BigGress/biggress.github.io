---
title: TypeScript 入门（3）——类
date: 2016-04-29 17:24:31
tags:
---

在编程过程中，我们会经常使用基于类的面向对象方法，在es5中我们要定义一个类必须使用prototype而在es6中定义了class让类的定义更加接近编程思想

* ### 类
我们先来看看下面这个例子
``` javascript
class Person{
    name:string;
    constructor(name:string){
        this.name = name
    }
    getName(){
        return "Hello,"+this.name
    }
}

let test = new Person("world");
```
在这里我对构造函数做了3件事情

1.定义了Person下name的数据类型
2.把构造时的name赋值给类的name值
3.定义了getName方法

最后，我们使用new构造FirstClass的实例，并进行初始化

* ### 类的继承
面向对象编程中最常用的就是基于类的继承扩张，让我们基于FirstClass做一个扩张
``` javascript
class Age extends Person{
    age:number;
    constructor(name:string,age:number){
        this.age = age;
        super(name)
    }
    getNameAndAge(){
        return this.name+":"+this.age
    }
}

let test = new Age("haha",123) 
```
这里一共做了4件事

1.Age先继承了Person
2.为age设置了类型
3.把构造的age赋值给age，然后通过super把name值传递给Person的name
4.定义了一个getNameAndAge方法

最后，创建一个test实例化一个Age

