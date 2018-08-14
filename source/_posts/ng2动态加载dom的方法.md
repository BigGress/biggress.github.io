---
title: ng2动态加载dom的方法
date: 2016-07-17 21:33:10
tags:
---

最近，想写一些自己的组件库，所以研究下了ng2的动态加载dom的方式，之所以用动态加载是为了，让其他使用者可以直接通过依赖服务，来启动组件不需要额外的操作。

不同于js动态加载dom，动态加载组件化必须经过框架的解释器（resolver）才能渲染成我们所写的组件实例。

做ng2的动态加载组件需要了解2个api，ComponentResolver和ViewChild

### 引入

``` typescripit
    import {ComponentResolver,ViewChild} from "@angular/core";
```

ComponentResolver中的resolveComponent方法，负责解释组件
ViewChild中的createComponent，负责将组建渲染到dom中

### 首先创建2个简单的组件
``` typescripit
    @Component({
        select:"dom1",
        template:"<div #dom1></div>"
    })
    export class Dom1Component{
    }

    @Component({
        select:"dom2",
        template:"<div>这里是dom2</div>"
    })
    export class Dom2Component{}
```

### 从dom1中动态加载dom2
我们需要稍微修改下dom1的Dom1Component
``` javascript
    export class Dom1Component implements AfterViewInit{
        @ViewChild("viewDom",{read:ViewContainerRef}) viewdom;

        constructor(private comres:ComponentResolver){}

        ngAfterViewInit(){
            this.comres.resolveComponent(Dom2Component).then((factory)=>{
                this.viewdom.createComponent(factory);
            })
        }
    }
```

之所以在AfterViewInit中加载dom，是因为ViewChild必须要等到dom加载完成之后才可以获取到这个viewDom

到此，我就可以在dom1中动态加载dom2  


更多细节可以查看ng官网
[ComponentResolver](https://angular.io/docs/ts/latest/api/core/index/ComponentResolver-class.html#!)
[ViewChild](https://angular.io/docs/ts/latest/api/core/index/ViewChild-var.html)

