#　2-JQuery效果

JQuery有很多效果，例如隐藏，显示，切换，滑动，淡入淡出，以及动画等等。



## 隐藏和显示

隐藏和显示效果分别使用hide()和show()方法。

语法:

$(*selector*).hide(*speed,callback*);

$(*selector*).show(*speed,callback*);

上面两个参数都是可选的

speed参数规定隐藏/显示的速度，可以取以下值："slow"，"fast"或毫秒

callback参数是隐藏或者显示**完成后执行**的函数名称，也叫回调函数



使用toggle()方法可以来切换hide()和show()方法，如果元素已经隐藏，那么该方法会显示元素，反之亦然。该方法同样有两个可选参数。



## 淡入淡出

淡入：fadeIn()

淡出：fadeOut()

切换淡入淡出：fadeToggle()

渐变为给定的不透明度：fadeTo()



### fadeIn()方法

该方法用于淡入隐藏的元素。

语法：$(*selector*).fadeIn(*speed,callback*);

可选的参数speed规定效果时长，可取值与上述一致，不再重复

可选的参数callback参数是fading完成后执行的函数名称



### fadeOut()方法

该方法用于淡出可见的元素。

语法：$(*selector*).fadeOut(*speed,callback*);

可选的参数speed规定效果时长，可取值与上述一致，不再重复

可选的参数callback参数是fading完成后执行的函数名称



### fadeToggle()方法

该方法用于可以在fadeIn和fadeOut方法之间切换，如果元素已经淡出，那么该方法会添加淡入效果，反之亦然。

语法：$(*selector*).fadeToggle(*speed,callback*);

可选的参数speed规定效果时长，可取值与上述一致，不再重复

可选的参数callback参数是fading完成后执行的函数名称



### fadeTo方法

该iafangfayunxu渐变为给定的不透明度(值介于0与1之间)

语法：$(*selector*).fadeTo(*speed,opacity,callback*);

**必需的speed参数规定效果时长，可取值与上述一致，不再重复**

必需的opacity擦书将淡入淡出效果设置为给定的不透明度，0到1之间

可选的参数callback参数是fading完成后执行的函数名称



## 滑动

向下滑动：slideDown()

向上滑动：slideUp()

切换向上和向下滑动：slideToggle()

### slideDown()方法

该方法可向下滑动指定元素。

语法：$(selector).slideDown(speed, callback);

可选的参数speed规定效果时长，可取值与上述一致，不再重复

可选的参数callback参数是滑动完成后执行的函数名称



### slideUp()方法

该方法可向上滑动指定元素。

语法：$(selector).slideUp(speed, callback);

可选的参数speed规定效果时长，可取值与上述一致，不再重复

可选的参数callback参数是滑动完成后执行的函数名称



### slideToggle()方法

该方法可以在slideDown和slideUp方法中进行切换，如果元素已经向下滑动了，那么slideTogge会向上滑动，反之亦然。

语法：$(selector).slideUp(speed, callback);

可选的参数speed规定效果时长，可取值与上述一致，不再重复

可选的参数callback参数是滑动完成后执行的函数名称



## 动画

JQuery的animate()方法允许创建自定义动画

语法：$(*selector*).animate({*params*}*,speed,callback*);

**必需的params参数定义形成动画的CSS属性**

可选的参数speed规定效果时长，可取值与上述一致，不再重复

可选的参数callback参数是动画完成后执行的函数名称



实例1：将div元素向右移动250个像素

```javascript
$("button").click(function(){
    $("div").animate({left:'250px'});
});
```

实例2：操作多个属性

```js
$("button").click(function(){
  $("div").animate({
    left:'250px',//将元素向右挪动250个像素
    opacity:'0.5',//不透明度变为0.5
    height:'150px',/高度为150px
    width:'150px'//宽度为150px
  });
});
```

实例3：使用相对值

```javascript
$("button").click(function(){
  $("div").animate({
    left:'250px',//向右挪动250px，使用绝对值
    height:'+=150px',//以当前高度为基准，再增加高度150px
    width:'+=150px'//以当前宽度为基准，再增加宽度150px
  });
});
```

实例4：使用预定义的值

可以把属性的动画中设置为show，hide或者toggle

```javascript
$("button").click(function(){
  $("div").animate({
    height:'toggle'//toggle是预定义的值，在这里意思是如果显示了该div元素，就隐藏如果隐藏了，那么就显示
  });
});
```

实例5：使用队列功能

JQuery提供针对动画的队列功能，意思是如果编写多个animate调用，那么JQuery会创建这些方法调用的内部队列，然后逐一运行这些调用。



**注意：animate方法几乎可以操作任何CSS属性，但是，当使用animate时，必须用Camel标记法书写所有属性名，就是有"-"的要去掉，例如padding-left要写成paddingLeft,margin-right要写成marginLeft；并且，色彩动画不包含在核心JQuery库中，要使用，需要下早颜色动画插件。**



## 停止动画

stop方法用来停止动画或者效果，在它们完成之前。stop方法适用所有JQuery效果函数包括滑动，淡入淡出和自定义动画。

语法：$(*selector*).stop(*stopAll,goToEnd*);

可选的stopAll采纳数规定是否应该清除动画队列，默认为false：即停止活动的动画，允许任何排入队列的动画向后执行

可选的goToEnd参数规定是否立即完成当前动画，默认为false



## 回调函数

许多JQuery函数有回调函数参数，包括动画函数。

回调函数在当前动画完成后执行。

例子：

有回调函数：警告框会在隐藏后显示

```javascript
$("button").click(function(){
  $("p").hide("slow",function(){
    alert("段落现在被隐藏了");
  });
});
```

无回调函数：警告框会在隐藏效果完成前弹出

```javascript
$("button").click(function(){
  $("p").hide(1000);
  alert("段落现在被隐藏了");
});
```



##　Chaining(链)

链允许在一条语句中运行多个JQuery方法(在相同的元素上)。

语法是在一个方法后直接再使用 "."来链接下一个方法。

例如把slideDown方法和slideUp方法链接到一起：`$("#p1").slideUp().slideDown();`

可以添加很多个方法调用，语句很长时，可以使用换行缩进，JQuery会抛掉多余空格。