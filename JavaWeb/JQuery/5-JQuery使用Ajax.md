# 5-JQuery使用Ajax

使用Jquery来发送异步请求。有几个常用的方法：`load()`、`get()`、`post()`等。



## load方法

JQuery的load方法是从服务器中加载数据，并把返回的数据放入到被选元素中。

语法：
`$(selector).load(url, data, callback)`

参数：

- url：规定希望加载的URL
- data：与请求一同发送的字符串键值对集合，格式为`{key:value,...}`
- callback：是load方法完成后执行的函数名称

其中可选参数callback是load方法完成后的回调函数，可以设置不同参数：

- responseTxt：调用成功后返回结果内容
- statusTXT：调用的状态
- xhr：包含XMLHttpRequest对象



## get方法

get方法通过GET请求给服务器发起请求。

语法：
`$(selector).get(url, data, success(response, status, xhr), dataType)`

参数：

- url：访问地址
- data：可选，连同请求发送到服务器的数据，键值对集合
- success：请求发送成功后运行的函数，额外的参数有：
    - response：来自请求的结果数据
    - status：请求状态
    - xhr：XMLHttpRequest对象
- dataType：期望服务器返回的数据类型，如果不指定JQuery会智能判断，可能的类型
    - "xml"
    - "html"
    - "text"
    - "script"
    - "json"
    - "jsonp"



例子：
使用get方法发送一个异步请求。返回json数据。

js代码：

```javascript
        $.get("some.do", function (data) {
            alert("姓名:" + data.name + ", 年龄:" + data.age);
        })
```

控制器方法：

```java
    @RequestMapping(value = "/some.do")
    public void doSome(HttpServletResponse response) throws IOException {
        Student student = new Student("张三", 20);
        ObjectMapper mapper = new ObjectMapper();
        String json = mapper.writeValueAsString(student);
        response.setContentType("application/json;charset=utf-8");
        PrintWriter pw = response.getWriter();
        pw.println(json);

    }
```

输出：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201216113930625.png" alt="image-20201216113930625" style="zoom:50%;" />



## post方法

和get方法，用法参数都一样，不讲解。







## ajax方法

这个方法是JQuery的底层AJAX实现。其他所有的ajax方法，如刚才讲的get，post都是在这个方法的基础上，进行封装。

可以调用该方法来设置关于异步请求的所有参数。

该方法有一个键值对集合参数settings，在这个集合中设置异步请求配置。

集合中常用参数：

- url：请求地址
- type：请求方式
- data：请求携带的参数，键值对集合
- dataType：期望返回的数据类型
- contentType：发送信息至服务器的内容编码类型，默认为"application/x-www-form-urlencoded"
- success：请求成功后的回调函数
- async：Boolean类型，默认值为true。即所有请求为异步请求。false为同步请求
- timeout：设置请求超时时间，单位为毫秒。此设置将覆盖全局设置
- cache：Boolean类型，是否 缓存页面。默认为true，即缓存页面

例子：

```javascript
        $.ajax({
            url:"some.do",
            type:"get",
            dataType:"json",
            success:function (data) {
                alert(data);
            }
        })
```





## 其他方法

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201216115230833.png" alt="image-20201216115230833" style="zoom:80%;" />







