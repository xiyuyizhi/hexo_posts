---
title: 原生js获取位置、寬高属性值
date: 2017-04-28 18:40:50
tags: javascript
thumbnail: /css/images/compare_width.png
---

![](/css/images/compare_width.png)

### Element.getBoundingClientRect()

返回元素的大小及其相对于视口的位置

> rectObject = object.getBoundingClientRect();
返回值是一个 DOMRect 对象,这个对象是由该元素的 getClientRects() 方法返回的一组矩形的集合。
DOMRect 对象包含了一组用于描述边框的只读属性——left、top、right和bottom，单位为像素。
除了 width 和 height 外的属性都是相对于视口的左上角位置而言的。


### Element.getClientRects()

*返回一个指向客户端中每一个盒子的边界矩形的矩形集合*


### clientHeight、offsetHeight、clientTop、scrollTop

>clientHeight | clientWidth  clientHeight = css height + css padding
offsetHeight | offsetWidth  offsetWidth = css width + padding +border +  vertical scrollbar   

offsetTop | offsetLeft
 **returns the distance of the current element relative to the top of the offsetParent node**



clientTop | clientLeft

**the top border、left border of an element in pixels**

Element.scrollTop | Element.scrollLeft

**The Element.scrollTop property gets or sets the number of pixels that 
the content of an element is scrolled upward**

---

*注意scrollWidth scrollHeight*

Element.scrollHeight

**The Element.scrollHeight read-only property is a measurement of the height of an element's content, 
including content not visible on the screen due to overflow.** 
 
Element.scrollWidth

**The Element.scrollWidth read–only property returns either the width in pixels 
of the content of an element or the width of the element itself, whichever is greater. 
If the element is wider than its content area (for example, if there are scroll bars for scrolling through the content), 
the scrollWidth is larger than the clientWidth.**

---

### window scrollX | scrollY | screenX | screenY | innerWidth | innerHeight

>innerWidth、innerHeight //window视图的寬高
 screenX、screenY  //浏览器左边、上边分别距离屏幕左、上的距离
 scrollX、scrollY  //document被滚动的距离 window.pageYOffset == window.scrollY


**For cross-browser compatibility, use window.pageYOffset instead of window.scrollY** 
跨浏览器使用:
window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop   
    
