# Ajax

Ajax是用来做局部刷新的，即在不重新加载刷新整个页面的同时，只更新页面的一部分数据。局部刷新使用的核心对象是异步对象：XMLHttpRequest，这个异步对象是存在在浏览器的内存中的。使用JS语法来创建该对象。

## AJAX异步实现步骤

1. 创建对象方式

    `var xmlHttp = new XMLHttpRequest();`

2. 给异步对象绑定事件：onreadystatechange

    当异步对象发起了请求，获取了数据都会触发这个事件，这个事件指定一个函数，处理状态的变化。

    `xmlHttp.onreadystatechange=function(){}`

3. 初始化异步请求对象

    异步对象有一个open方法，xmlHttp.open(请求方式get |post, 访问地址，同步|异步请求(默认为true，即异步))

    例如`xmlHttp.open("get", "loginServlet?name=zxpwd=123", true);`

4. 使用异步对象发送请求

    xmlHttp.send(str)：当用POST方式发请求会使用str参数

5. 获取服务端返回的数据，使用异步对象的属性：responseText



XMLHttpRequest有三个重要属性：

1. onreadystatechange属性：一个函数，每当readyState属性改变时，会调用该函数
2. readyState属性：表示XMLHttpRequest请求的状态，从0到4发生变化
    - 0：请求未出初始化，创建异步请求对象
    - 1：初始化异步请求对象，xmlHttp.open(请求方式，请求地址，true)
    - 异步对象发送请求，xmlHttp.send()
    - 异步对象介绍应答数据，从服务器端返回数据，XMLHttpRequest内部处理
    - 异步对象已经将数据解析完毕，此时才可读取数据
3. status属性：
    - 200：‘OK’
    - 404：未找到页面



**注意：open函数的URL请求的地址是不加/的servlet名称，例如Servlet1的url-pattern为/one，则参数填one即可**

**注意：如果使用POST请求向服务端传参数，那么必须设置RequestHeader，为httpRequest.setRequestHeader("Content-type", "application/x-www-form-urlencoded");，如果没有，那么后台就无法获取参数，并且设置请求头要在open函数执行完后**

**注意：open方法的最后一个参数，true代表异步请求：即使用异步对象发起请求后，不需要等待数据的返回，即可进行其他操作**





## 使用JSON格式传递数据

有四种处理JSON的工具库，分别为Gson(Google的)，fastjson(阿里巴巴的)，Jackson，还有一个忘了。

我用的是jackson。

首先将jackson的jar导入项目依赖。

### 将Java对象转换为Json格式的字符串

1. 创建ObjectMapper对象：`ObjectMapper om = new ObjectMapper()`
2. 使用该对象的  String writeValueAsString(Object value)方法将Java对象转换为json格式的字符串，Java对象的对应变量名称为json中的key，实际值就为json格式的value
3. 设置响应头的Content-type为“application/json;charset=utf-8”
4. 然后使用响应头的PrintWriter将该字符串写入

### 在js中解析该字符串

使用js中JSON的prase解析该字符串，返回一个json对象，使用 . 运算符 后面跟json的name即可访问对应value

或使用js的eval函数，前后分别加"("和")"，例如 var jsonObj = eval("()" + json + ")");，返回一个json对象