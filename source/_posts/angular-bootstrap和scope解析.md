---
title: angular bootstrap和scope解析
date: 2017-05-24 18:16:51
tags: angular
---


- ## angular启动过程

- ## scope

---

## angular启动过程

1. ### 定义

> angular.module提供创建组件的方法，是一系列任务的集合。每个模块内部维护_invokeQueue数组

    ```javascript
        angular.module('app',[])
        moduleInstance={
            name:,
            requires:,
            constant:,
            provider:,
            factory:,
            value:,
            service:,
            filter:$filterProvider服务的方法,
            directive:$compileProvider服务的方法,
            controller:$controllerProvider服务的方法,
            component:$compileProvider服务的方法,
            config:,
            run: 
        }

    ```

内置的ng模块 包含angular框架的核心服务($compile,$rootScope等)

2. ### 注册

> createInjector() 遍历模块及其依赖，执行_invokeQueue中定义的各种组件

```javascript
    angular.bootstap=function(ele,modules){
        modules=modules||[]
        modules.unshift('ng')
        createInjector(modules)
    } 

```
![](/css/images/angular-img/di.png)

3. ### compile DOM, DOM linked to scope

$compile

```javascript
    var injector=createInjector(modules);
    injector.invoke([$compile,$rootScope,function($compile,$rootScope){
        $compile($element)($rootScope);
    }]);
```

> This also means that at this point the initial scope hierarchy of the application is constructed.  Any directives that use inherited or isolate scopes will cause new scopes to be created and linked. All of that is handled by the compiler.   

![](/css/images/angular-img/directive-execute-order.png)

![](/css/images/angular-img/compile.png)

4. ### 初始运行一次脏检测。

```javascript
    var injector=createInjector(modules);
    injector.invoke([$compile,$rootScope,function($compile,$rootScope){
        $rootScope.$apply(function(){
             $compile($element)($rootScope);
        })
    }]); 
```

## scope

渲染数据到模板的上下文，model需要挂载在scope上。

view和controller之间的桥梁

controller: 初始化$scope,为$scope添加行为

![](/css/images/angular-img/MVVM.png)



scope做的事：

-  模型和视图的桥梁 数据绑定 脏检测
-  scope层级关系，作用域继承
-  广播、监听事件



### dirty-checking


 ```javascript

    .controller($scope){
        $scope.user.name=''
        $scope.$watch('user.name',function(){ 
        })
    }

 ```

> 内部维护$$watchers数组 存放  $watch(watchFN,listenFN) 

  ```javascript

    $$watchers.push({
        watchFn:watchf,
        listenerFn:listenf
    })

  ```   

> $digest 迭代$$watchers数组,对每一条watcher,比较监听的变量前后两次值是否相同，不同则执行监听函数。

  ```javascript

    Scope.prototype.$digest = function() {
        var self = this;
        var newValue, oldValue;
        _.forEach(this.$$watchers, function(watcher) {
            newValue = watcher.watchFn(self);
            oldValue = watcher.last;
            if (newValue !== oldValue) {
                watcher.last = newValue;
                watcher.listenerFn(newValue, oldValue, self);
            }
        });
    };

  ``` 
> 只要有变化，脏检测就会一直循环执行，直到所有监听的值都不在改变。

  ```javascript

        Scope.prototype.$$digestOnce = function() {
            var self = this;
             var newValue, oldValue, dirty;
            _.forEach(this.$$watchers, function(watcher) {
                newValue = watcher.watchFn(self);
                oldValue = watcher.last;
                if (newValue !== oldValue) {
                    watcher.last = newValue;
                    watcher.listenerFn(newValue,
                    (oldValue === initWatchVal ? newValue : oldValue), self);
                    dirty = true;
                 }
            });
                                                                                                        return dirty;
        };

        Scope.prototype.$digest = function() {
            var dirty;
            do {
                dirty = this.$$digestOnce();
            } while (dirty);
        };

  ```

$evel   以当前scope作为上下文执行

  ```javascript

        Scope.prototype.$eval = function(expr, locals) {
            return expr(this, locals);
        };

  ```

  $apply 
  ```
    Scope.prototype.$apply = function(expr) {
        try {
            return this.$eval(expr);
        } finally {
            this.$digest();
        }
    };
  ```

### 作用域继承

$rootScope.$new()

```javascript

    Scope.prototype.$new = function() {
        var ChildScope = function() { };
        ChildScope.prototype = this;
        var child = new ChildScope();
        this.$$children.push(child);
        child.$$watchers = [];
        child.$$children = [];
        return child;
    };

```

> ngController创建新的作用域，每一个controller构造函数执行的时候都会传入一个新创建的子作用域

>$digest() 只有当前scope及其子scope的$$watchers执行

```javascript

   Scope.prototype.$$everyScope = function(fn) {
        if (fn(this)) {
            return this.$$children.every(function(child) {
            return child.$$everyScope(fn);
        });
        } else {
            return false;
        }
    };
```

> $apply  goes directly to the root and digests the whole scope hierarchy
  
```javascript

Scope构造函数中有$root属性指向当前作用域（$rootScope）
Scope.prototype.$apply=function(expr){
    try {
            return this.$eval(expr);
        } finally {
            this.$root.$digest();
        }
}  
 ```

独立作用域

> isolated scope 不会继承任何属性从父类scope,

### 作用域事件

![](/css/images/angular-img/pub-sub.png)





