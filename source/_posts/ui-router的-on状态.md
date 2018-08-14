---
title: ui-router的$on状态
date: 2016-01-25 22:51:11
tags:
---

# 关于ui-router下$on状态的总结

使用$on状态之前...我们要先明白这些状态的作用是什么...在angular启动路由的时候...会触发路由的几个事件...如路由开始解析、路由解析成功等等的状态。我们可以根据不同的状态执行不同的回调函数...处理不同的事件。

### 一、在明白$on的作用后...我们还要明白怎么触发这些状态...

    * 1、$rootScope下触发
        ``` bash
            $rootScope.$on("$stateChangeStart",function(event, toState, toParams, fromState, fromParams){ ... });
        ```
        在$rootScope下触发$on...正如$rootScope的作用一样...它会在所有的控制器中启用这个回调函数。

    * 2、$scope 下触发
        ``` bash
            $scope.$on("$stateChangeStart",function(event, toState, toParams, fromState, fromParams){ ... })
        ```
        在$scope下触发$on...它只会在当前的控制器中启用这个回调函数。

### 二、所有$on的状态
    | 状态    | 中文    |
    | ------- | :----:  |
    | $stateChangeStart |  当模板开始解析之前触发|
    | $stateNotFound | 当模板没有被找到 |
    | $stateChangeSuccess | 当模板解析完成后触发 |
    | $stateChangeError | 当模板解析过程中发生错误时触发 |
    | $viewContentLoading | 当视图开始加载 |
    | $viewContentLoaded | 当视图加载完成 |
