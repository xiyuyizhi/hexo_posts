---
title: angular-component
date: 2016-10-03 18:17:09
tags: angular
---

angular中定义directives,components的一些最佳实践,结合ES6写法

- [component](#component)
    
    - [controller](#controller)
    
        - [生命周期钩子](#生命周期钩子)
        
    - [单向数据流和事件](#单向数据流和事件)
    
    - [routed components](#routed-components)
    
- [directive](#directive)


## component

  - ### controller

      1. 控制器应该只用在component定义中

      2. 使用es6 class定义controller,在constructor中进行依赖注入
    
         ```
            .component('testComp',{
                template:'',
                contorller:class Ctrl{
                
                    constructor($http,$timeout){
                     'ngInject'   
                    }
                }
            })
        ```


     -  #### 生命周期钩子
   
        1. 初始化代码放在$onInit中
   
        2. 在$onChanges中监听bindings数据变化,对传入的数组或对象copy一份以打断内外组件的数据交互
   
        3. 事件处理放在$postLink中
   
            ```
                bindings:{
                    nList:'<'
                },
                controller:class Ctrl{
            
                    $onChanges(change){
            
                        if(change.nList){
                              this.innerList=Object.assign({},this.nList)
                         }
            
                     }  
                }
             ```
   
  -  ### 单向数据流和事件
    
    bindings中不在使用'=',数据的传递只使用‘<’和‘@’,并且在$onChanges中打断数据的连接
    子组件中要和父组件进行数据交互采用事件方式
    
    ```
        
        .component('parentComp',{
            template:'<child-comp n-list='$ctrl.list' n-add-item='$ctrl.addItem($event)'></child-comp>'
            controller:class ParentCtrl{
                
                $onInit(){
                    this.list=[]
                }
                
                addItem({item}){
                    this.list=this.list.concat([item])
                }
                
            }
        })
        
        .component('childComp',{
            bindings:{
                nList:'<',
                nAddItem:'&'
            },
            controller:function(){
                
                $onInit(){
                    this.news={
                        content:''
                    }
                }
                $onChanges(changes){
                    if(changes.nList){
                        this.list=angular.copy(this.nList)
                    }
                }
                add(){
                    this.nAddItem({
                        $event:{
                            item:this.news
                        }
                    })
                }
                
            }
        })
        
    ```
  - ### routed components
  
    routed components就是一个‘父组件’,它内部所需要的数据不是bindings其他组件中的数据。
    不同于自己调用service查询出来，数据是通过路由resolve获取到的
    
    ```
        
        .component('someComp',{
        
            bindings:{
                todoList:'<'
            },
            controller: class someCtrl{
                ....
            }    
        })
        .service('someService',class SService{
            ....
        })
        .config($stateProvider => {
        
            $stateProvider.state('someState',{
                url:'someUrl',
                component:'someComp',
                resolve:{
                    todoList: someService => {
                        return someService.getTodoList()
                    }
                }
            })
        
        })
    ```
  
    
## directive

   只用directive定义属性 ’A‘
   不使用scope
   
   ```
        定义directived的class写法
        
        export class TodoAutoFocus {
          constructor($timeout) {
            'ngInject';
            this.restrict = 'A';
            this.$timeout = $timeout;
          }
          link($scope, $element, $attrs) {
            $scope.$watch($attrs.todoAutofocus, (newValue, oldValue) => {
              if (!newValue) {
                return;
              }
              this.$timeout(() => $element[0].focus());
            });
          }
        }
        
        .directive('todoAutofocus', () => new TodoAutoFocus())
    
   ```
