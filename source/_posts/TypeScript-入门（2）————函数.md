---
title: TypeScript 入门（2）——函数
date: 2016-04-29 17:20:25
tags:
---

* ### 为函数定义类型
``` javascript
function add(x:number,y:string):string{
    return x+y;
}
let myAdd = function(x:number,y:string):string{
    return x+y
}
```
这里可以看出我们为函数的变量和函数分别定义了不同类型
* ### 完整的函数类型
``` javascript
let myAdd:(x:number,y:number)=>number=
    function(x:number,y:number):number{return x+y}
```
这是函数的完整写法，如果函数没有定义类型一般typescript会进行推断，判断出一个可能的值
* ### 可选参数
``` javascript
function bind(first:string,last?:string){
    if(last){
        return first + " " +last
    }else{
        return first
    }
}
```
这个函数和普通函数不同的地方就在于第二个参数后面多了个问号，这样就可以表示这个参数是可有可无
* ### 默认参数
``` javascript
function bindTest(first:number=123){
    return first
}
```
这里我们为函数的第一个参数定义了一个默认值，如果函数没有参数传入，那么first就会被默认为123
* ### Lambda表达式和this
this是编程中的非常常用的写法，但是js函数中的this常常指向window，通过Lambda就可以把this直接只想函数所在的对象
``` javascript
var deck = {
        name:"test",
        getName:function(){
            return ()=>{
                return this.name
            }
        }
    }
```
我们看看这里我们写了什么，首先是定义了一个对象，对象上有name属性，getName拥有一个回调方法调用了Lambda函数，这个函数的this就直接指向了deck对象，所以最后getName就会输出test