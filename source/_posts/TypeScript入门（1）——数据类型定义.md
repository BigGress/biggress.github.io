---
title: TypeScript入门（1）——数据类型定义
date: 2016-04-29 17:16:39
tags:
---

typescript的数据类基本与javascript几乎相同

* ### 布尔值

我们可以定义一个数据的类型为boolean

``` javascript
let isDone: boolean;
```
当然我也可以设置数据的默认值

``` javascript
let isDone:boolean = true;
```
又或者我们可以定义一个布尔类型的数组和默认值
``` javascript
let isDone:boolean[];
let isDone:boolean[] = [true,false];
```

* ### number
我们可以定义一个数字类型和字符串类型和上面的布尔类型一致

``` javascript
let isNumber:number;
let isNumber:number = 1;
let isNumberArr:number[];
let isNumberArr:number[] = [1,2,3];
```

``` javascript
let isString:string;
let isString:string = "haha";
let isStringArr:string[];
let isStringArr:string[] = ["a","b"];
```

* ### 数组
数组类型除了上面的定义方式以外还可以这样子定义

``` javascript
let isArr:Array<string>;
let isArr:Array<string>=["a","b"];
```
在<>内可以包含各种数据类型

* ### enum
这是js数据类型的一种补充，使数组可以有一个友好的名字，

``` javascript
enum Color {Red,Green,Blue};
let c:Color = Color.Green;
```
这时c就会等于1

默认情况下，编号是从0开始，我们也可以手动指定起始值
``` javascript
enum Color {Red=1,Green,Blue}
let c:Color=Color.Green
```
这时c就会等于2

枚举的好处是你可以由枚举值获得它的名字
``` javascript
enum Color {Red=1,Green,Blue};
let name:string = Color[2];
```
这是name就是Green
* ### 任意值
有时候很多编程阶段不清楚数据的类型会是什么，我们可以直接指定变量为any;

``` javascript
let notSure:any = 4;
notSure = "string test";
notSure = false
```
甚至你可以为any定义一个数组
``` javascript
let AnyArr:any[]=[1,true,"abc"];
console.log(AnyArr[2])
```
* ### 空值
空值的主要意义在于为数据设置返回值

``` javascript
function test():void{
    console.log("test")
}
```
它也可以为变量说明数据类型
``` javascript
let isVoid:void = undefined;
let isVoid2:void = null;
```