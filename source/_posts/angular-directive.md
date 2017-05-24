---
title: angular-directive
date: 2016-10-06 18:16:02
tags: angular
---

- [指令介绍](#instruct)
- [使用介绍](#params)
	- [scope](#scope)
	- [controller](#controller)
	- [required](#required)
	- [transclude](#transclude)
- [最佳实践](#best)

---

<h3 id='instruct'>指令介绍</h3>
 指令是angular中十分重要的概念,能让我们创建自定义元素,不仅是元素,还可以是（属性,注释,css class）,一般也就是自定义元素或属性。
 创建方法 angular.module().directive()。1.5以上版本提供了directive()的语法糖component(),只用来创建组件。

---

```js    
    angular中各部分执行顺序
    | config块
    | run块   
    | 指令 comile阶段
    | controller 按着父子DOM结构从外到内
    | 指令 controller阶段
    |     包括$onInit $onChanges $doCheck $onDestroy $postLink生命周期钩子
    |      construtor --> $onChanges --> $onInit --> (若binding数据更新,onChanges还被执行) --> $postLink 
    | 指令link pre阶段 从父指令到子指令
    | 指令link post阶段 从子指令到父指令  
```

<h3 id='params'>使用介绍</h3>

- <h5 id='scope'>scope</h5>
    - scope:false
      不创建自己的scope，直接继承父类scope
    - scope:true
       创建自己的作用域，但会继承父scope 
    - scope:{}
       独立的作用域
       
- <h5 id='compile'>compile</h5>

      处理一些不需要作用域的操作，DOM处理等，返回link函数。
      注意，对当前使用的模板中的指令只会编译一遍，所以此阶段常用来做一些优化
        
- <h5 id='link'>link</h5>

      link:function(scope,element,attr,transclude)
      可以访问scope,用来注册事件处理函数等 
      注意，处在循环中的指令，链接函数在每次循环中都运行

- <h5 id='controller'>controller 生命周期钩子</h5>

    *controller被实例化在pre-linking阶段之前,并且能够被其他指令访问*
    controller中提供了一些钩子函数，在directive的生命周期的特定阶段被调用
   
    - $onInit
        在DOM元素上的所有controller初始化完成之后,会在每个controller上被调用.在这个阶段,bindings中的数据初始化
        (注意,初始化但可能为空，等待异步操作完成后的填充，可以理解为$onInit是bindings中数据的constructor)
        顾名思义，这个阶段就是做一些初始化操作,$onInit在pre,post link函数之前执行
        $onInit和require 
           require的父组件中定义的方法可以在$onInit中使用
    - $onChanges(changesObj)
         在 < @绑定的数据更新时被调用,changesObjs是一个对象，键是所绑定的属性的名，值为
         ```
         {
                     currentValue,
                     previousValue, 
                     isFirstChange()
         }
          ```
         形式的对象。
         onChanges钩子的一个十分重要的作用是clone bindings中的数据，来打断与外层组件的数据共享(因为 虽然 < 是单向绑定,但传递的是引用,在组件内部对传递进来的数据做修改，
         是会污染外层数据的)
         ```
           {
             bindings:{
              nList:'<'
             },
             controller:function(){
             
                this.$onChanges=function(changes){
                    if(changes.nList){
                      this.inNList=angular.copy(this.nList)
                    }
                }
                
             }
           }
         ```
    - $onDestroy
        组件的作用域被销毁的时候被调用,在这个方法中我们可以释放多余的资源,监听器和相关时间处理函数
        onDestory方法的触发是自上而下的，意味着父组件先执行，子组件后执行
        
    - $postLink
        在当前元素及其下元素都被link之后调用，这个方法可以被用来添加DOM事件处理函数和直接的DOM操作。
        需要注意的时，子元素中包含templateUrl的指令在此时还没有被编译和链接,要等到template异步加载完之后才触发这两个阶段。

- <h5 id='required'>required</h5>

    用来require其他的指令或component,注入他们的controller,  
    对directive方法来说,是注入到link方法中，作为第四个参数
    对component方法来说,是通过controller的this来访问
   
   ```
    require://可以是string,array,object,
                //如果是object,形式为
                 {
                    parentDir:'?XX | ^XX | ^^XX | ?^ | ?^^'
                 }
        
                directive('childDir',function(){                            
                     return {
                           require:'^parentDir', //require父指令contorller中定义的方法
                           require:'wrapCtrl', //require外层中的controller
                           link:function(scope,elemnt,attr,parentDir){
                              parentDir.XXXX()
                           }
                    }   
                })
                
                component('childComp',{
                
                    require:'^parentComp',
                    controller:function(){
                       this.$onInit=function(){
                         this.parentComp.XXXX() //调用父组件中的方法，注意是在onInit方法中使用的，
                                                //因为此时当前DOM及外层的controller都初始化完毕
                       }
                    }
                })  
   ```
- <h5 id='transclude'>transclude</h5>

  transclude用来提取一部分dom元素并把他们copy到其他地方，值得注意的是，这部分dom元素连接的scope还是原来地方的作用域。
主要用在把指令中包裹的DOM内容插入到指令的template中的指定位置。好处就是这部分dom还保持着原来的scope,

 
<h3 id='test'>最佳实践</h3>

上面介绍了声明指令用到的一些常用属性，但directive方法应该不再用来自定义元素。
directive只用来定义属性。组件用component来定义

Here are some advisories for using Directives:

Never use templates, scope, bindToController or controllers
Always restrict: 'A' with Directives
Use compile and link where necessary
Remember to destroy and unbind event handlers inside $scope.$on('$destroy', fn);
only use transclude: true when you want to create a directive that wraps arbitrary content.
use controller when you want to expose an API to other directives. Otherwise use link.

使用component方法定义组件的注意点：

 不再使用=传递数据
 单向数据流和事件
 bindings数据在onChanges中复制一份
 
  ```
    ES6写法
     .component('com',{
        template:'',
        binding:{
          list:'<',
          nCallback:'&'
        }
        controller: classChildCtrl(){
            
            $onInit(){
                    
            }
            
            $onChanges(){
                this.myList=angular.copy(this.list)
            }
            
            doSomething(){
                this.nCallback({
                    $event:{
                        data:someData
                    }
                })
            }
            
        }
     })
     
     <comp list='$ctrl.list' n-callback='$ctrl.somefn($event)'></comp>
     
  ```