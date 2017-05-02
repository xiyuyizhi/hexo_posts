---
title: es6学习笔记(二)
date: 2017-05-02 18:39:28
tags: es6
---

- [解构赋值](#解构赋值)
- [set](#Set)
- [map](#Map)

## 解构赋值

解构赋值用来提取对象或数组中的数据到不同的变量中。

 - 数组解构

    数组解构的使用场景：
    
    1、 变量赋值

    ```
        let arr=[3,5,7]
        let [one,two]=arr
        console.log(one) //3
        console.log(two) //5

        结合rest patten的使用
        let activities=['watch TV','dance' ，’play‘]
        const [first,...rest]=activities
        console.log(first) // watch TV
        console.log(rest) //['dance','play'
        ]
    ```

    2、 交换变量

    ```
        let prev=4
        let next=5
        [prev,next]=[next,prev]

    ```

 - 对象解构

    对象解构的使用场景：

    1、变量赋值

    ```
        let o = {p: 42, q: true};
        let {p, q} = o;
        console.log(p); // 42
        console.log(q); // true

        *注意()的使用*
        let p,q
        ({p,q}=o)
        console.log(p); // 42
        console.log(q); // true

        比较: 使用对象进行解构赋值的时候,如果赋值操作左边不是变量声明，则整个的赋值运算要放到()内部,因为没有申明的话{p,q}会被当成代码块而不是对象字面量。
        

    ```
    2、分配默认值

    ```
        let {a=5,b=1}={a:10}
        console.log(a) //10
        console.log(b) //1
    ```

    3、为function的参数设置默认值

    ```
        function drag( {radius=50, point={x:0, y:0}} ){
                console.log(radius,point.x,point.y)
        }

        drag({
            point:{
                x:100,
                y:100
            }
        })  
        // 50,100,100

        完全使用默认值
        drag({})

    ```
## Set

Set中存储的值只能是唯一的，不管是基本类型的还是对象类型的。我们可以按元素插入的顺序来迭代Set。

 #### value equality

对于Set来说,-0和+0是不相等的，NaN和undefined也可以存储在Set中,并且NaN==NaN,虽然在js中NaN!==NaN

 #### 常用属性和方法

    size : 元素的number
 
    add(value)

    clear() : 清除所有元素

    delete(value) ： 删除特定元素

    has() : return boolean

    entries() ： 返回一个包含每一个元素的数组的新的迭代对象

    ```

        let mySet=new Set([1,2,3,4])
        for(const [key,value] of mySet.entries()){
            //key values是一样的
            console.log(key) // 1,2,3,4
         }

    ```

    keys() values() :一样，返回一个包含所有元素的可迭代对象

    ```
        const mySet=new Set([1,2,3,4,5,6,7])
        for(let item of mySet.keys()){
             console.log(item)
        }
        for(let item of mySet.values()){
            console.log(item)
         }
     ```


    Set Array之间的相互转换

    ```
        array--->set
        const arr=['v1','v2','v3']
        const set =new Set(arr)

         set-->array
        const arr1=Array.from(set)
        console.log([...set])

     ```


## Map

key/value的映射,任何类型的值都可以用做key或value。注意对象只能string,symbols做key。

 #### 属性方法

    size()  set(key, value)  get(key)  has(key)  clear() 

    delete(key)   entries()   keys()  values()

    forEach(callbackFn[, thisArg])

    迭代:
    ```
        myMap.forEach(function(value, key) {
            console.log(key + ' = ' + value);
        });

        for (var [key, value] of myMap) {
            console.log(key + ' = ' + value);
        }

        for (var key of myMap.keys()) {
            console.log(key);
        }

         for (var value of myMap.values()) {
            console.log(value);
        }   

        for (var [key, value] of myMap.entries()) {
            console.log(key + ' = ' + value);
        }

    ```