# 3-Layui复杂内容

## 表单form

基本结构：

```html
<form class="layui-form" action="">
    <div class="layui-form-item">
      <label class="layui-form-label">标签区域</label>
      <div class="layui-input-block">
        原始表单元素区域
      </div>
    </div>
</form>
```

首先用要在js中加载form模块。

然后在form标签的class中加入`layui-form`。layui表单才可以生效。

在写表单各个组件时，要按照上面的基本结构。

每一行要在 `<div class="layui-form-item"></div>`中。

其中，输入元素要在`<div class="layui-input-block"></div>`或者`<div class="layui-input-inline"></div>`

中。

例如

```html
<form class="layui-form" action="">
    
  <div class="layui-form-item">
    <label class="layui-form-label">输入框</label>
      
    <div class="layui-input-block">
      <input type="text" name="title" required  lay-verify="required" placeholder="请输入标题" autocomplete="off" class="layui-input">
    </div>
  </div>
    
  <div class="layui-form-item">
    <label class="layui-form-label">密码框</label>
      
    <div class="layui-input-inline">
      <input type="password" name="password" required lay-verify="required" placeholder="请输入密码" autocomplete="off" class="layui-input">
    </div>
    <div class="layui-form-mid layui-word-aux">辅助文字</div>
  </div>

    
  <div class="layui-form-item">
      
    <div class="layui-input-block">
      <button class="layui-btn" lay-submit lay-filter="formDemo">立即提交</button>
      <button type="reset" class="layui-btn layui-btn-primary">重置</button>
    </div>
  </div>
    
</form>
```



### 输入框

格式为：`<input type="text" name="" required lay-verify="required" class="layui-input">`

多加了`required lay-verify="required" class="layui-input"`。

其中：

- required：表示必填字段，如果没填，那么会出现Layui设置的默认提示信息。如果想自定义提示信息，可以不用。
- lay-verify：需要验证的类型，可以取：email、number、date、required、identity、pass、title、content、url、phone。然后layui可以自动验证输入的内容是否符合对应类型。也可以不用。
- class="layui-input"必须要加上



### 下拉选择框

```html
<select name="city" lay-verify="">
  <option value="">请选择一个城市</option>
  <option value="010">北京</option>
  <option value="021">上海</option>
  <option value="0571">杭州</option>
</select> 
```

其中第一项是用来占个坑，提示信息。没有value。可以自定义文本。

- 可以使用selected属性来设定默认选中项
- 可以使用disabled来禁用某个项
- 使用lay-search属性来开启，搜索匹配。也是layui内置的。

还可以使用optgroup来分组：

```html
<select name="quiz">
  <option value="">请选择</option>
  <optgroup label="城市记忆">
    <option value="你工作的第一个城市">你工作的第一个城市？</option>
  </optgroup>
  <optgroup label="学生时代">
    <option value="你的工号">你的工号？</option>
    <option value="你最喜欢的老师">你最喜欢的老师？</option>
  </optgroup>
</select>
```



### 复选框

正常使用复选框即可。

默认风格是：<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103210012227.png" alt="image-20210103210012227" style="zoom:53%;" />

如果想用原始风格，使用`lay-skin="primary"`就可以。

- 属性title可以自定义显示文本
- 属性checked可以设定默认选中
- 设置value值来自定义返回值,否则是默认的on



### 开关

其实就是checkbox的变形。在复选框中设定`lay-skin="switch"`形成按钮风格。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210103210309295.png" alt="image-20210103210309295" style="zoom:50%;" />

- 属性checked可设定默认开启
- disabled开启禁用
- lay-text自定义开关两种状态的文本，例如`lay-text="ON|OFF"`
- 设定value值，否则返回默认on



### 单选框

和使用普通单选框radio一样。

- title属性自定义文本
- checked属性默认选中
- disabled开启禁用



### 文本域

使用标签`<textarea name="" class="layui-textarea">`可以设置一个文本域。



### 表单方框风格

通过在form标签的class中追加`layui-form-pane`，来设定表单的方框风格。

并且要在复选框、开关、单选框这些组合的`class="layui-form-item"`对应的标签中添加pane属性，其他不变。



例如：

```html
<form class="layui-form layui-form-pane" action="">
  <div class="layui-form-item" pane>
    <label class="layui-form-label">单选框</label>
    <div class="layui-input-block">
      <input type="radio" name="sex" value="男" title="男">
      <input type="radio" name="sex" value="女" title="女" checked>
    </div>
  </div>
</form>
```



### 表单动态操作

#### 更新渲染

有时候，表单元素可能是动态插入。

这时候需要使用`form.render(type, filter)`来更新渲染。

第一个参数是表单的type类型，可选，如果没有就是对全部类型的表单进行刷新。

可取值，即可局部刷新的type有：

- select：选择框
- checkbox：复选框，含开关
- radio：单选框

第二个参数是filter，就是class="layui-form"所在的元素的属性lay-filter的值，

例如：

```html
【HTML】
<div class="layui-form" lay-filter="test1">
  …
</div>
 
<div class="layui-form" lay-filter="test2">
  …
</div>
      
【JavaScript】
form.render(null, 'test1'); //更新 lay-filter="test1" 所在容器内的全部表单状态
form.render('select', 'test2'); //更新 lay-filter="test2" 所在容器内的全部 select 状态
//……
```



#### 事件监听

form模块在layui事件机制中注册了专属事件。

语法是：`form.on('事件(过滤器值)'， callback函数)`

form支持的事件有：

| 事件     | 描述                       |
| -------- | -------------------------- |
| select   | 监听select下拉选择事件     |
| checkbox | 监听checkbox复选框勾选事件 |
| switch   | 监听checkbox复选框开关事件 |
| radio    | 监听radio单选框单击事件    |
| submit   | 监听表单提交事件           |

默认情况，如果不指定过滤器值，那么事件监听的是全部的form模块元素。如果只想监听一个元素，使用事件过滤器，也就是`lay-filter`指定的值。



##### 监听select选择

选择框被选中时触发，回调函数返回一个object参数。

例如：

```javascript
form.on('select(filter)', function(data){
  console.log(data.elem); //得到select原始DOM对象
  console.log(data.value); //得到被选中的值
  console.log(data.othis); //得到美化后的DOM对象
}); 
```

##### 监听checkbox复选框

复选框被勾选时触发，回调函数返回一个object参数。

```javascript
form.on('checkbox(filter)', function(data){
  console.log(data.elem); //得到checkbox原始DOM对象
  console.log(data.elem.checked); //是否被选中，true或者false
  console.log(data.value); //复选框value值，也可以通过data.elem.value得到
  console.log(data.othis); //得到美化后的DOM对象
});  
```

##### 监听switch开关

开关被点击时触发，回调函数返回一个object参数。

```javascript
form.on('switch(filter)', function(data){
  console.log(data.elem); //得到checkbox原始DOM对象
  console.log(data.elem.checked); //开关是否开启，true或者false
  console.log(data.value); //开关value值，也可以通过data.elem.value得到
  console.log(data.othis); //得到美化后的DOM对象
});  
```

##### 监听radio单选

单选框被点击时触发，回调函数返回一个object参数。

```javascript
form.on('radio(filter)', function(data){
  console.log(data.elem); //得到radio原始DOM对象
  console.log(data.value); //被点击的radio的value值
});  
```



##### 监听submit提交

按钮被点击或者表单被执行提交时触发。其中回调函数只有在验证全部通过(就是用lay-verfiy指定的值)才会进入，回调函数返回一个obejct参数。

```javascript
form.on('submit(*)', function(data){
  console.log(data.elem) //被执行事件的元素DOM对象，一般为button对象
  console.log(data.form) //被执行提交的form对象，一般在存在form标签时才会返回
  console.log(data.field) //当前容器的全部表单字段，名值对形式：{name: value}
  return false; //阻止表单跳转。如果需要表单跳转，去掉这段即可。
});
```

*号代表任意值，要用提交按钮的`lay-filter`值替换。





#### 表单赋值、取值

语法：`form.val(filter, object)`。

用于给指定的表单集合的元素赋值和取值。如果object参数存在，就是赋值；不存在，就是取值。

obejct是一个键值对集合。

例如：

```javascript
//给表单赋值
form.val("formTest", { //formTest 即 class="layui-form" 所在元素属性 lay-filter="" 对应的值
  "username": "哈哈哈" // "name": "value"
  ,"sex": "男"
  ,"auth": 3
  ,"check[write]": true
  ,"open": false
});
 
//获取表单区域所有值
var data1 = form.val("formTest");
```



