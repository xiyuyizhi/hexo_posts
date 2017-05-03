---
title: es6学习笔记(一)
date: 2016-09-02 16:06:22
tags: es6
---

- [箭头函数](#箭头函数)
    - [new.target](#new-target)
- [class](#class)

## 箭头函数

箭头函数相对于函数表达式是一种更简洁的语法，箭头函数没有自己的 *this, arguments, super, new.target*,并且 **不能被用做构造函数**。

 1. 不绑定this

    ```
	class Test {

		constructor() {
			this.age = 3
			this.arr = [1, 2, 3, 4, 5]
		}

		filterArr() {
            //箭头函数，this指的就是class Test的一个实例对象
			 return this.arr.filter((item) => {
			  	return item !== this.age
			 })

             //普通函数,this是undefined
			return this.arr.filter(function (item) {
				console.log(this)
				return item !== this.age
			})
		}

	}

	const t = new Test()
	console.log(t.filterArr())

    ```

    **invoked through call,apply**
    Since this is not bound in arrow functions, the methods call() or apply() can only pass in parameters. this is ignored

 2. 不绑定arguments

    箭头函数不绑定arguments，所以函数内的arguments指向的是外层函数的arguments。

    ```
        function foo() {
            var f = (i) => arguments[0] + i; // foo's implicit arguments binding
            return f(2);
        }
        foo(1); // 3
    ```

    箭头函数获取参数的替代方式

    ```
        function foo() { 
            var f = (...args) => args[0]; 
            return f(2); 
        }
    ```


#### new.target

通过new.target可以判断一个function或constructor是否通过new关键字调用。对于实例来说，new.target指向构造函数，普通函数的new.target是undefined。

使用场景
```
    function Foo() {
        if (!new.target) throw 'Foo() must be called with new';
        console.log('Foo instantiated with new');
    }

    Foo(); // throws "Foo() must be called with new"
    new Foo(); // logs "Foo instantiated with new"
    
```

## class