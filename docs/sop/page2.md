---
description: 前端面试题复习-基础知识
title: 前端面试题复习二
readingTime: false
tag:
 - 前端基础
recommend: 1
---

* 页面导入样式时，使用link和@import有什么区别？

    答：1.link是HTML标签，@import是css提供的。

    2.link引入的样式页面加载时同时加载，@import引入的样式需等页面加载完成后再加载。

    3.link没有兼容性问题，@import不兼容ie5以下。

    4.link可以通过js操作DOM动态引入样式表改变样式，而@import不可以。

* DOM节点至少包含哪些属性？

    答：DOM中的Node至少拥有nodeType、nodeName和nodeValue这三个基本属性。

* html的元素有哪些（包含H5）？

    1.块级元素：div h1-h6 p ol ul li table tbody td tr thead

    2.行内元素： a span input button link em i label img

    3.h5新增元素： audio vidoe canvas header aside footer nav section article

* css3有哪些新增的特性？

    答：边框圆角border-radius 阴影 box-shadow 2d3d转换 transform:rorate scale skew translte 过渡动画 transition 鼠标事件pointer-events 滤镜 filter 渐变 linear-gradient 弹性盒子模型 flexbox 多媒体查询 @media

* css选择器有哪些?

    答：通配选择器 * id选择器 class选择 标签选择 伪类选择器 伪元素选择器 属性选择器 兄弟选择器 + 后代选择器>

* 在页面上隐藏元素的方法有哪些？

    答：1 使用绝对定位将元素移出可视范围

    2 使用 overflow:hidden隐藏超出的部分

    3 使用dispaly:none.缺点是会将元素移出文档流

    4 使用 opactity:0 将透明度设置为0

    5 使用visibility:0 设置为不可见

    6 使用transform:scale(0)将元素缩小为不可见

* 简述超链接target属性的取值和作用？

    答：_top和_praent一般用户iframe中，多iframe可用framename指定

    值描述

    _blank在新窗口中打开被链接文档。

    _self默认。在相同的框架中打开被链接文档。

    _parent在父框架集中打开被链接文档。

    _top在整个窗口中打开被链接文档。

    framename在指定的框架中打开被链接文档。

* 简述js的基本数据类型？

    答：六种基本数据类型，undefined,Null,Boolean,number,String,Symbol,bigInt

* 常用的js内置对象有哪些？

    答：数学对象Math 时间对象Date 正则表达式 RegExo, 数字对象Number, 数据结构对象Map Set，JSON, 异步控制Promise 基本对象Object等等

* 简述js的原型、原型链机制

    答：js中每个对象都有一个原型对象。原型对象是一个包含可供改对象使用的属性和方法的对象，这些对象属性和方法可以被改对象的实例所共享，由此实现属性和方法的继承。

    原型对象可通过prototype和__proto__访问和设置，也可通过es新增的Object.getPrototype()方法访问原型。

    当访问一个对象的方法或属性时，首先将访问对象自身的方法和属性，若未找到相应的方法或属性，将试图访问上一级原型链上的方法，若依旧未找到，继续寻找上层原型，直到找到或根原型不存在此方法。

* js作用域链和this指向判断方法？

    答：js作用域链是指js在执行中访问对象和函数的顺序和方式。JS作用域链由函数作用域和全局作用域组成。

    函数内部申明的变量将仅拥有函数作用域，它可以访问同函数内部的变量和方法，也可访问父级的函数作用域和全局作用域中的方法和属性，而全局和其他无关函数无法访问到它。当在函数内部访问变量或属性时，优先访问当前作用域的方法和方法，若未找到，将依次寻找父级的方法和变量，直到全局作用域。优先级高的方法和变量将覆盖优先级低的方法和变量。

    全局作用域链是默认的作用域，它包含了所有的函数作用域和变量。

    关于this：

    在全局范围内，this指向全局对象（window或global）。

    在函数内部，this指向调用该函数的对象。如果函数作为函数调用，则this指向全局对象。

    在对象方法中，this指向该对象。

    在构造函数中，this指向新创建的对象。

    在使用call()或apply()方法调用函数时，this指向传递给call()或apply()方法的第一参数。

    在使用箭头函数时，this指向定义函数时的作用域。

* 关于css3 flex布局的理解和使用，以及一些常用属性的说明？

    flex布局会把容器变成弹性容器，弹性容器内的元素将按照设置的属性自动排列，并且可以自适应父容器的大小。flex常用属性有:

    display: flex; - 将父元素设置为弹性容器。

    flex-direction: 设置元素排列的主轴方向，默认未。

    justify-content: center; - 设定子元素在主轴上的对齐方式，默认为flex-start。

    align-items: center; - 设定子元素在交叉轴上的对齐方式，默认为flex-start。

    flex-wrap: wrap; - 控制子元素是否换行,默认不换行。

    flex:1 设置子元素占父级的总量，资源将按照flex的比例排列。

    order:1 定义了项目的排列顺序，数值越小，排列越靠前，默认值为0。

    flex-shrink:定义了当空间不足时，子元素的缩小比例，当设置为0时，子元素不缩小。

