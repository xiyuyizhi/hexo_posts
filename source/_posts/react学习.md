---
title: react学习
date: 2017-05-11 15:48:38
tags:  react
---



## JSX介绍

JSX是一种js的语法扩展，比较像xml，html，最终会被转化成regular js Objects。

使用JSX
```
    var Hello = React.createClass({
     render: function() {
     return <div>Hello {this.props.name}</div>;
    }
});
ReactDOM.render(
  <Hello name="World" />,
  document.getElementById('container')
);
```
不使用JSX
```
  var Hello = React.createClass({
    displayName: 'Hello',
    render: function() {
        return React.createElement("div", null, "Hello ", this.props.name);
    }
});

ReactDOM.render(
  React.createElement(Hello, {name: "World"}),
  document.getElementById('container')
);

```
使用JSX，代码看起来更整洁，清晰。

##使用JSX渲染html标签和react组件

渲染html标签时使用小写的标签名
渲染组件，组件名首字母大写

Notes:因为JSX更接近js,react dom使用驼峰命名规范命名属性，class在JSX中用className表示，
tabindex变成tabIndex。

## JSX表示对象

Babel编译JSX编译成React.createElement()形式，

```
    const element = (
      <h1 className="greeting">
       Hello, world!
      </h1>
    );
    编译成：
    const element = React.createElement(
      'h1',
      {className: 'greeting'},
      'Hello, world!'
    );  
    React最终会生成
    // Note: this structure is simplified
    const element = {
      type: 'h1',
      props: {
        className: 'greeting',
        children: 'Hello, world'
      }
    };
    这样的对象，这些对象叫做“React element”。react使用这些对象来构造dom,虚拟DOM也是基于
    这种对象计算的。

```