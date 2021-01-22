# 5-RestFul和SpringMVC常用注解

在SpringBoot中使用SpringMVC和直接使用SpringMVC没有任何不同，完全一样。

有几个常用的注解。

## 常用注解



### Controller

SpringMVC的注解，处理http请求。

### RestController

SpringMVC4.0的新增注解，是@Controller注解功能的增强，相当于@Controller和@ResponseBody的组合注解。

如果一个Controller添加了@RestController，那么就相当于该Controller的所有方法都添加了@ResponseBody注解。

主要用于返回字符串或JSON数据。

### RequestMapping

支持Get和Post请求。

### @GetMapping

只支持Get请求，别的请求方式会报错，405。Get请求主要用于查询操作。

### @PostMapping

只支持Post请求，别的请求方式会报错，405。Post请求主要用于新增操作。

### @PutMapping

只支持Put请求，别的请求方式会报错，405。Put请求主要用于修改数据。

### @DeleteMapping

只支持Delete请求，别的请求方式会报错，405。Delete请求主要用于删除数据。



## RESTFul

REST是一种互联网软件架构设计的风格。但它不是标准的，只是提出了一个客户端和服务器交互时的架构理念和设计原则。基于这种理念和原则设计的接口可以更简洁，尺寸更小。任何技术都可以实现这种理念，如果一个架构符合REST原则，就称其为RESTFul架构。

这种架构简单来说，就是：

- 接口命名使用名词，不要有动词。

    例如/student，/users等都输入RESTFul架构。而/addStudent，/deleteUser则不属于。

- 使用Http的方法来表示不同操作。

    因为没有动词在接口名称中，所以使用HTTP方法来表示要对数据进行的操作。一般来说：

    1. Get方法表示查询或者请求数据
    2. Post方法表示新增数据。(也可以表示修改数据)
    3. Put方法表示修改数据。
    4. Delete方法表示删除数据

- 使用status状态码来表示请求的结果。



例如一个Http接口：`http://localhost/boot/order?id=1021&status=1`，

那么采用RESTful风格则http地址应为：`http://localhost/boot/order/1021/status=1`



**将参数转换为请求路径。**

### 优点

1. 轻量，直接基于Http，而不需要任何别的消息协议。
2. 面向资源，一目了然。
3. 数据描述简单，一般以xml，json做数据交换
4. 简单，低重组。

### RESTFul注意的地方

1. 请求路径中不要出现动词
2. 分页，排序等操作，一般使用参数来进行传递



采用RESTful风格要使用的一个注解是：@PathVariable

该注解可以将URL中的占位符参数{xxx}绑定到处理器类方法的形参@PathVariable("xxx")中。

例如：

一个处理器方法是：

```java
    @GetMapping(value = "/student/{id}")
    public Object getStudent(@PathVariable("id") Integer id) {
        return studentService.queryStudentById(id);
    }
```

那么当浏览器发送请求比如：`http://localhost/student/1010`时，就会把1010这个值绑定到处理器方法的形参id上。

只要保证注解@PathVariable中属性值和请求路径中占位符参数值一致。

当然也可以绑定多个，例如：

```java
    @GetMapping(value = "/student/{id}/{name}")
    public Object getStudent(@PathVariable("id") Integer id,@PathVariable("name") String name) {
        return studentService.queryStudentById(id);
    }
```

这样，加上之前讲的SpringMVC的常用注解，就可以实现RESTful风格。





**注意：当出现请求冲突，即一个请求路径对应了两个处理器方法，那么解决方法一是修改请求路径，二是修改请求方法。**

