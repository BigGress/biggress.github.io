---
title: ng2－－生命钩子
date: 2016-09-18 22:11:16
tags:
---

ng2是一个组件化的框架，那么组件化必然带有钩子函数，今天就来说说，ng2的生命钩子函数(Lifecycle Hooks)。

生命周期是什么？每个组件和指令都有生命周期，从创建、渲染、再销毁，这就完成了组件的一个生命周期。而生命周期钩子，就是让开发者可以介入这些生命周期的一个或者多个钩子函数。

通用的钩子函数:

| 函数名   | 作用 |
| -------- | :-------: |
| ngOnInit | 初始化组件或者指令 |
| ngOnChanges | 当绑定的属性发生变化时  |
| ngDoCheck   | 检测组件所有的变化     |
| ngOnDestroy | 组件被销毁时 |

由于组件内部可以有子组件，所以组件拥有一些特殊的组件钩子函数：

| 函数名 | 作用 |
| -------- | :-------: |
| ngAfterContentInit | 把数据投入组件内部之后（比如ng-content投入之后） |
| ngAfterContentChecked | 当数据都必定完成之后 |
| ngAfterViewInit | 创建组件的视图 |
| ngAfterViewChecked | 检查完组件视图的绑定后调用 |

下面我们来测试下，[项目代码](http://plnkr.co/edit/xRUgMsplqudlgxv4DCSM?p=preview)(一言不和就上代码)

组件钩子函数执行顺序及其次数;

![执行图](/css/images/liftcycle_1.png);

| 函数名 | 顺序 | 次数 |
| ------| ---| --- |
| OnInit | 0 | 1 |
| DoCheck | 1 | 2 |
| AfterContentInit | 2 | 1 |
| AfterContentChecked | 3 | 2 |
| OnChanges | 4 | 1 |
| AfterViewInit | 5 | 1 |
| AfterViewChecked | 6 | 2 | 

从上图我们可以这样子的结论，在组件创建的时候，所有的check钩子都执行了2次，其它的Init和OnChanges方法则只执行了一次。
这里要单独说明的时OnChanges和OnDestroy方法:


#### OnChanges
> - OnChanges方法只有在父组件往子组件输入的值发生变化的时候才会触发，所以在组件初始化的时候触发了一次事件，当输入组件的值发生变化之后，OnChanges就开始调用。

#### OnDestroy
> - OnDestroy是一个组件销毁后才会执行的钩子函数，所以在页面上的组件消失就会触发。


还有一组比较混淆的方法就是AfterView和AfterContent，相对来讲业务组件中AfterView应该比AfterContent更加常用。
那么两个区别在那里呢？
#### AfterView
> - AfterView是在组件及其子组件创建完成之后就会触发的方法。

#### AfterContent
> - AfterContent则内容投影完成之后才触发的，就是在ng-content的内容完成之后才会触发的方法，所以对于底层组件来说ng-content的更加常用。
