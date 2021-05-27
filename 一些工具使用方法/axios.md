# axios

axios官方文档：`https://www.kancloud.cn/yunye/axios/234845`

讲一下axios的使用方法。

axios和之前的jquery和js传统的异步请求最大的不同是：axios要注意传递参数设置Content-Type的问题。

不同的传参方式要后台要使用不同方法来接受。



axios每个请求都有一个方法：

![image-20210309123515870](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210309123515870.png)



## GET

形式：

```javascript
axios.get('url', {
    params:{
        //请求参数，如id:123,name:'test'等
    }
}).then(function(respone){
    //发送请求收到相应要做的事情
})
```



GET方式发送请求没有Content-Type，因为GET请求的参数是添加在URL后的。

也就是参数在请求行，不再请求体。所以没有Content-Type。



## POST

形式：

```javascript
axios.post('/user', {
    /请求参数
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```



axios默认发送post请求的Content-Type是`application/json`，也就是以json格式发送。

后台接收时要使用@RequestBody注解来进行接收

并创建相应的类。

例如：

发送post请求：

```javascript
                axios.post('/admin/category/test',{
                        id:1,
                        name:'test'
                }).then(function (data) {
                    layer.msg('请求发送成功')
                })
```



那么在后台要首先创建对应接收类：

```java
package com.blog.core.model;

import lombok.Data;

@Data
public class Test {

    private Integer id;
    private String name;
}

```



然后在控制器方法形参加注解@RequestBody，形参类型为Test

如：

```java
    @PostMapping("/test")
    public void post(@RequestBody Test test) {
        System.out.println("id === " + test.getId() + "\n" + "name === " + test.getName());
    }
```

就可以接收到参数。



### 使用application/x-www/form-urlencoded

如果想使用Content-Type="application/x-www/form-urlencoded"接收参数。

就要将参数json参数转为key、value型。

使用qs.js库。

首先引入qs.js文件。

然后使用`Qs.stringify(data)`,其中`data = {key:value, key:value...}`

该方法可以将json格式字符串转变为`key=value&key=value`型参数

例如：

```javascript
let data =  {id:1, name:'test'};               
axios.post('/admin/category/test',Qs.stringify(data))
    .then(function (data) {
    layer.msg('请求发送成功')
                })
```

或直接`Qs.stringify({id:1, name:'test'})`

后台接收参数的方式就可以为逐个接收，不需要单独创建Java对象

即：

```java
    @PostMapping("/test")
    public void post(Integer id, String name) {
        System.out.println("id === " + id + "\n" + "name === " + name);
    }
```



这样就可以接收到参数





## PUT

形式：

```javascript
axios.put('/user', {
    /请求参数
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

和post形式一样。

axios默认发送PUT请求的Content-Type也是`application/json`，那么接收参数也许需要创建对应类，并加上注解@RequestBody。

如果想要发送Content-Type="application/x-www-form-urlencoded"，也一样使用qs即可。



反正axios的PUT和POST基本一样



## DELETE



形式：

```javascript
axios.delete('url', {
    params:{
        //请求参数，如id:123,name:'test'等
    }
}).then(function(respone){
    //发送请求收到相应要做的事情
})
```

和GET请求形式差不多，默认axios也会把DELETE的参数添加到URL中。后台逐个参数接收即可。



如果想以`application/json`形式来传参，那么需要改写：

```javascript
axios.delete('url', {
    data:{
        //请求参数，如id:123,name:'test'等
    }
}).then(function(respone){
    //发送请求收到相应要做的事情
})
```

并且后台要使用@RequestBody注解并创建对应Java对象。



















