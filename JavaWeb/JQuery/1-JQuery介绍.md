# 1-JQuery介绍

JQuery是js的一个工具库，封装了js代码。简化了开发流程。

##　JQuery的基本语法

JQuery语法是通过选取HTML元素来执行一些操作。

基础语法：$(selector).action()

使用美元符号 “$”来进行选取，选择符selector查询和查找HTML元素，action执行对元素的操作

选择器和CSS差不多，有这么几种

- 标签元素选择器：使用标签名称来获取元素数组，例如 $("p") ，表示获取所有<p>
- id选择器：使用唯一的id来选取指定元素，例如 $("#test")，表示获取id为test的元素，注意 "#"
- .class选择器：使用类选择器来选取元素，例如 $(".test")，获取所有class为test的元素，注意"."
- 括号中可以直接填DOM对象，例如 `var Obj = document.getElementById("test");$(Obj).hide()`

除此之外，还可以通过属性来选取，

$("[attr]")：表示获取所有 有attr属性的元素



更多实例：

- $("*")：选取所有元素
- $("this")：选取当前元素
- $("p.intro")：选择class为intro的p元素
- $("p:first")：选择第一个p元素
- $("[href]")：选取带有href属性的元素
- $("a[target='_blank']")：选取所有target属性值为"_blank"的<a>元素
- $("a[target!='_blank']")：选取所有target属性值不为"_blank"的<a>元素
- $(":button")：选取所有type="button"的<input>元素和<button>元素





## DOM对象和JQuery对象

使用js语法创建的对象就是DOM对象，使用JQuery语法创建的对象叫做JQuery对象。**注意：JQuery表示的对象都是数组。即使某些数组只有一个元素。**

DOM对象和JQuery对象可以互相转换：

DOM对象转为JQuery对象：语法，$(DOM对象)

JQuery对象转为DOM对象：语法，从数组中获取第一个对象，第一个对象就是DOM对象，使用[0]或者get[0]



转换是为了使用指定对象的属性和方法。



## 入口函数的几种写法

在js中，要将js代码写在`window.onload() = function(){}`中，类似的，在JQuery中，也有几种写法。

- 使用`$(document).ready(function(){})`，将代码写到function函数中，用$符号将document转换为JQuery对象，使用该对象的ready方法，类似window.onload
- 是上面的简洁写法，即`$(function({}))`，一般使用这种多一些

除此之外，`jQuery(function(){})`和`window.jQuery(function({}))`也是等价的。

 

### jQuery入口函数和JavaScript入口函数的区别

- jQuery的入口函数是在html所有标签都加载之后，就会去执行，可以执行多次，不会被覆盖
- JavaScript的window.onload时间是等到所有内容，包括外部图片之外的文件夹在完后，才会去执行，只能执行一次，如果执行第二次，第一次的执行会被覆盖



 