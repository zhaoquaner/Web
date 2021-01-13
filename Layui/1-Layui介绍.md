# 1-Layui介绍



Layui是一个前端UI框架，非常容易使用。

下载layui，目录结构为：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103192605190.png" alt="image-20210103192605190" style="zoom:80%;" />

只需要导入其中两个文件layui.css和layui.js。

layui把不同功能分成不同模块，在使用之前，要首先使用`layui.use('模块名称',function(){})`来加载。

其中，laydate和layer模块可以独立出来，单独使用。laydate模块是日期选择模块，layer是弹出层模块。



## 使用

加载模块的方法，使用：

`layui.use('模块名称', function(){})`

其中：

- 第一个参数是模块名称，如果一次加载多个模块，可以使用`['module1', 'module2']`来加载多个模块
- 第二个参数是回调函数，可以理解为入口函数。我们把自己关于js代码写在这个函数中。

在回调函数中，要首先定义刚才加载的模块对应的模块变量。然后再使用。

举个例子，使用表单form模块：

```js
layui.use('form', function(){
    let form = layui.form;
    //自己的代码
})
```

如果加载多个模块：

```javascript
layui.use(['form', 'layer'], function(){
    let form = layui.form;
    ley layer = layui.layer;
    //自己的代码
})
```





主要就使用`layui.use()`。



### 其他的方法



`layui.link(href)`：加载动态css文件，href是css文件路径



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103193827397.png" alt="image-20210103193827397" style="zoom:67%;" />



## Layui公共类和属性

布局相关：

1. `layui-inline`：用于将标签设为内联块状元素
2. ·`layui-main`：设置一个宽度为1140px的水平居中块(无响应式)

辅助：

1. `layui-icon`：用于图标
2. `layui-elip`：用于单行文本溢出省略
3. `layui-unselect`：用于屏蔽选中
4. `layui-disabled`：设置元素不可点击状态
5. `layui-circle`：设置元素为圆形
6. `layui-show`：显示块状元素
7. `layui-hide`：用于隐藏元素

文本：

1. `layui-text`：定义一段文本区域，该区域特殊标签，如a、li等会进行相应处理
2. `layui-word-aux`：灰色标注性文字，左右会有间隔

背景色：

1. `layui-bg-red`：赤色背景
2. `layui-bg-orange`：橙色背景
3. `layui-bg-green`：墨绿色背景，主色调
4. `layui-bg-cyan`：藏青色背景
5. `layui-bg-blue`：蓝色背景
6. `layui-bg-black`：经典黑色背景
7. `layui-bg-grey`：灰色背景



## HTML规范

Layui在解析html元素时，必须要保证结构是被支持的。如果改变了结构，会导致解析失败。所以应该详细查看元素模块的相关文档。



layui有一些自定义属性：

1. lay-skin：定义相同元素的不同风格。如checkbox的开关风格`lay-skin="switch"`(好像，主要也就用在这)。
2. lay-filter：事件过滤器，在监听自定义事件时会用到，可以看做一个ID选择器。
3. lay-submit：定义一个触发表单提交的button，没有值，只加上这个属性就行。

## Layui内使用JQuery

layui内置了jquery，所以不需要导入。

使用方法：

```javascript
layui.use('form', function(){
    let form = layui.form;
    let $ = layui.$;
    //自己的代码
})
```

在这里定义一句`let $ = layui.$;`就可以了。之后就正常使用Jquery。

