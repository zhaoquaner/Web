# JQuery DOM操作

JQuery提供了一系列与DOM相关的方法，可以访问和操作HTML元素和属性。



## 获取内容和属性

三个实用的用于获取内容的JQuery方法(**设置时，有参数；返回时，无参数**)：

- text()：设置或返回所选元素的文本内容
- html()：设置或返回所选元素的内容(包括HTML标记)
- val()：设置或返回表单字段的值，即value属性的值

获取属性的方法：

- attr(属性名称)：返回对应属性的值

    例如获取超链接中href的值：$("#a").attr("href")，就是获取id为a的超链接元素的href属性对应的值




## 设置元素内容

同样适用上面三个获取内容的方法：text()，html()，val()。

-  可以直接将希望设置的值传入该方法的参数，进行设置。

- 它们拥有回调函数，回调函数有两个参数：**被选元素列表中当前元素的下标(因为每个JQuery对象都是数组，索引从0开始)，以及原始值**(旧值)，然后使用return返回您希望使用的字符串，即函数新值。

    例子：

    ```javascript
    $(function () {
    
        $("#change").click(function () {
            $("p").text(function (i, oldText) {
                return "索引值为:" + i + "，旧文本为:" + oldText;
            })
        })
    })
    ```

    将原来的段落内容改为打印对应索引并再次输出旧文本，结果为：

    ![image-20200922181846914](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/20200922181854.png)

## 设置属性内容

上述的attr方法同样可以设置/改变属性值

- 可以直接向attr方法传入指定值，例如：`$("button").click(function(){ $("#baidu").attr("href", "http://www.baidu.com")}    )`，设置了id为baidu的超链接元素的href属性的值为百度的网站

- attr方法也允许设置多个属性：

    ```javascript
    $("button").click(function(){
        $("#baidu").attr({
            "href" : "http://www.baidu.com",
            "title" : "百度网址"
        });
    });
    ```



attr()也提供回调函数，回调函数有两个参数：**被选元素列表当前元素的下标，以及原始(旧)的值**，新值用return返回

```javascript
$("button").click(function(){
  $("#runoob").attr("href", function(i,origValue){
    return origValue + "/jquery"; 
  });
});
```



## 添加元素

有四种添加新元素的JQuery方法

- append()：在被选元素结尾插入内容
- prepend()：在被选元素开头插入内容
- after()：在被选元素之后插入内容
- before()：在被选元素之前插入内容



### append()方法

该方法在被选元素的结尾插入内容(仍然在该元素的内部)。

### prepend()方法

该方法在被选元素的开头插入内容。



可以通过前两个方法添加若干个新元素，append和perpend方法可以通过参数接收无限数量的新元素。

例子：

```javascript
function appendText(){
    var txt1="<p>文本-1。</p>";              // 使用 HTML 标签创建文本
    var txt2=$("<p></p>").text("文本-2。");  // 使用 jQuery 创建文本
    var txt3=document.createElement("p");
    txt3.innerHTML="文本-3。";               // 使用 DOM 创建文本 text with DOM
    $("body").append(txt1,txt2,txt3);        // 追加新元素
}
```



### after()方法和before()方法

after方法在被选元素之后插入内容。

before方法在被选元素之前插入新元素



### append/prepend和after/before的区别

用append:

```javascript
<p>
  <span class="s1">s1</span>
</p>
<script>
$("p").append('<span class="s2">s2</span>');
</script>

//结果是：
<p>
  <span class="s1">s1</span>
  <span class="s2">s2</span>
</p>
```

用after:

```javascript
<p>
  <span class="s1">s1</span>
</p>
<script>
$("p").after('<span class="s2">s2</span>');
</script>

//结果是这样的:

<p>
  <span class="s1">s1</span>
</p>
<span class="s2">s2</span>
```

总结：

- append/prepend是在被选择的元素内部嵌入
- after/before是在元素外面追加





## 删除元素

可以使用以下两个JQuery方法来删除元素：

- remove：删除被选元素和子元素
- empty：删除被选元素的子元素，也就是清空被选元素。

remove方法也可以接受一个参数，允许对被删除的元素进行过滤，该参数可以是任何JQuery选择器的语法。

例如：删除class='inalic'的p元素

$("p").remove(".inalic")；





## 获取并设置CSS类

JQuery有很多对CSS操作的方法：

- addClass()：向被选元素添加一个或多个类
- removeClass()：从被选元素删除一个或多个类
- toggleClass()：对被选元素进行 添加/删除类的切换操作
- css()：设置或 返回样式属性



先在css文件中定义两个类：

```css
.important {
	color:brown;
	font-size:30px
} 
.unimportant {
	color:red;
	font-size:15px;
}
```

现在就有important和unimportant两个类了。



### addClass()方法

该方法可以给多个元素添加多个css类，可以接受多个css类参数

例子：给h1和h2添加important类

```javascript
    $("#button").click(function () {
        $("h1,h2").addClass("important");
    });
```

![1](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/20200926094734.gif)



### removeClass()方法

删除指定的class属性。

例子：删除上述元素的important类属性

```javascript
    $("#remove").click(function () {
        $("h1, h2").removeClass("important");
    })
```

结果：

![2](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/20200926100652.gif)



### toggleClass()方法

该方法对被选元素进行添加/删除类的切换操作：

例子：

```javascript
    $("#button").click(function () {
        $("h1,h2").toggleClass("important");
    });
```

结果：

![3](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/20200926102152.gif)



### css()方法

css方法设置或者返回被选元素的一个或多个样式属性。

#### 返回CSS属性

语法为： `css("propertyname");`

例子：`$("p").css("background-color");`

#### 设置CSS属性

语法：`css("propertyname", "value");`

例子：`$("p").css("background-color", "yellow");`

#### 设置多个CSS属性

语法：`css({"propertyname":"value", "propertyname":"value",...});`

例子：`$("p").css({"background-color":"yellow", "font-size":"200%"})`



## JQuery尺寸方法

JQuery提供了很多处理尺寸的方法

- width()
- height()
- innerWidth()
- innerHeight()
- outerWidth()
- outerHeight()

### width和height方法

width方法用来设置或者返回元素的宽度(不包括内边距，边框或外边距)

height方法用来设置或返回元素的高度(不包括内边距，边框或外边距)

例子：

```html
<html>
<body>
    <div style="width: 200px;height: 200px; padding: 10px; border: 5px;background-color:burlywood" id="div"></div>
    <input type="button" id="button" value="显示尺寸" />
</body>
</html>
```

js代码：

```javascript
$(function () {
    $("#button").click(function () {
       let width = $("#div").width();
       let height = $("#div").height();
       let text = "<p>该div元素的宽度为：" + width  + "<br>该div元素的高度为：" + height + "</p>";
       $("#div").html(text);
    });

})
```

结果：

![4](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/20200926103743.gif)



### innerWidth和innerHeight方法

分别返回元素的高度和宽度(包括内边距)

### outerWidth和outerHeight方法

分别返回元素的宽度和高度(包括内边距和边框)