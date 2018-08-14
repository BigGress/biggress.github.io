---
title: ui-router
date: 2016-01-25 22:42:53
tags:
---

# ui-router使用

相比ngRouter...ui-router在很多方面做了优化...最主要的是实现了路由的嵌套...这是ngRouter所没有实现的

### 1.引入

``` bash
    //在模块内进行依赖...用的是ui.router
    var router = angular.module("router",["ui.router"]);

```

### 2.使用

* 设置首页地址和重定向地址

``` bash
    //进行angular的配置
    //在这里做个说明,之所以说明$urlRouterProvider...是为了压缩加密时不会报错...但是如果不加密就不用加$urlRouterProvider
    router.config(["$urlRouterProvider",
        function($urlRouterProvider){

        //用$urlRouterProvider来声明默认地址
        $urlRouterProvider.when("","/index");

        //当然通过$urlRouterProvider也可以做重定向...
        //意思是如果路径是/test时就自动重定向到/index去
        $urlRouterProvider.when("/test","/index")

        }
    ])
```

* 配置路由

``` bash
    //同样像$urlRouterProvider一样...我们需要先声明$stateProvider
    router.config(["$stateProvider",function($stateProvider){

        //在定义路由前...我们需要为每个路由添加一个名字
        //这个名字有2个作用，1、是给ui-sref转跳时使用
        //2、用来说明路由间的关系
        $stateProvider.state("index",{
            url:"/index",
            templateUrl:"./index.html",
            controller:"index"
        })

        //html
        <a ui-sref="index">index</a>

        =============================华丽的分割线=================================

        //如果我要定义一个index的子路由了话...就可以这样子写
        //ui-sref就可以设置为index.next了话...点击后就会跳转到/index/next这个路径下面

        //但是特别说明...这样子写子路由意味着页面嵌套，所以必须在index.html下增加一个
        //<div ui-view></div>

        $stateProvider.state("index.next",{
            url:"/next",
            templateUrl:"./next.hmlt",
            controller:"next"
        })

        //html
        <a ui-sref="index.next">next</a>
        <div ui-view></div>

    }])
```

* 在url设置参数

``` bash

    $stateProvider.state('index',{
        url:'/index/:test',
        templateUrl:'index.html',
        controller:'indexCtrl'
    })

    $stateProvider.state('seconed',{
        url:'/seconed/:num',
        templateUrl:'./tpls/test2.html',
        controller:'test2'
    })

    //设置参数

    //html
    <a ui-sref="seconed({test:'测试'})">link</a>

    //js
    ctrl.controller('preLink',['$scope','$state',function($scope,$state){
        $scope.click = function(){
            $state.go('seconed',{num:'abc'})
        }
    }])

    //在控制器中获取
    ctrl.controller('indexCtrl',['$statePatams',function($statePatams){
        console.log($statePatams.num)
    }])
```

* Query Parameters

``` bash

    //在router里面设置参数
    $stateProvider.state('index',{
            url:'/index?Id',
            templateUrl:'index.html',
            controller:'index'
    })

    //设置和获取参数的方法与上面相同！
```