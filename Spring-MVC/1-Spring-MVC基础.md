# 1-Spring-MVC基础

## 概念

SpringMVC是一个基于Spring的框架，是Spring的一个模块，专门用来作web开发。

web开发的底层是Servlet，框架是在Servlet的基础上加入一些功能，封装一些功能，使得web开发更加方便。

SpringMVC和Spring一样，能够创建对象，并放入到SpringMVC容器中。SpringMVC容器中放的是控制器对象。



SpringMVC中有一个Servlet类：DispatherServlet，**叫中央调度器**

​		它负责接受用户的所有请求，用户把请求给DispatherServlet，然后DispatherServlet把请求转发给Controller对象，由Controller对象处理请求。

DispatherServlet主要使用注解来进行开发，使用@Controller创建控制器对象，并把该对象放入SpringMVC容器中。当做一个Servlet对象，**但它不是Servlet，是一个普通类的对象，只是SpringMVC赋予了控制器对象额外的功能。**



## DispatherServlet

​		DispatherServlet是SpringMVC的一个核心类，叫做中央调度器，或者前端控制器。它把用户的请求转发给Controller对象，并把Controller返回的结果再返回给用户。

​		**同时，DispatherServlet创建的时候，会同时把SpringMVC容器也创建出来，把该容器放入ServletContext中，并读取SpringMVC配置文件中的内容，把Controller对象创建出来。**

​		需要在web.xml文件中注册该对象，格式为：

```xml
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern></url-pattern>
    </servlet-mapping>
```

要加上<load-on-startup>标签，来使该对象在服务器启动时就创建出来，同时创建出SpringMVC容器来。这个标签中应该填一个大于等于0的数，数越小，创建顺序的优先级越高。



其中<url-pattern>的值有两种类型：

- 使用拓展名方式，语法是`*.xxx`，其中xxx是自定义的拓展名，常用的有`*.do`、`*.action`、`*.mvc`等

    它表示，凡是以该拓展名结尾的全都交给这个中央调度器处理，例如值时`*.do`，那么url为http://localhost:80/myweb.some.do请求，以`.do`结尾，会交给该中央调度器处理

    使用斜杠`/`



在默认情况下，SpringMVC读取Springmvc配置文件的路径为/WEB-INF/<servlet-name>-servlet.xml。

也就是说，上面注册中央调度器的name加上`-servlet`，例如上面<servlet-name>是springmvc，那么默认读取配置文件路径为`WEB-INF/springmvc-servlet.xml`。

很不方便，因此**在<servlet>标签内**可以使用<init-param>标签来自定义springmvc配置文件位置，格式为：

```xmk
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:</param-value>
        </init-param>
```

<param-name>的值固定不变，classpath后加上自定义配置文件路径。

最终格式为：

```xml
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern></url-pattern>
    </servlet-mapping>
```







## 一个简单的例子

以一个很简单的例子来说明一下SpringMVC是如何使用的。

在前端页面发起一个请求，然后显示欢迎界面。

### 加入依赖

在新建的maven项目中加入spring-webmvc依赖，这个依赖同时也包含了spring的核心依赖(不包含spring-jdbc、spring-tx、spring-aspectj依赖)。同时加入servlet和jsp依赖。

### 在web.xml文件中注册DispatherServlet

使用前面讲的语法格式来注册中央调度器

```xml
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>
```



### 创建发起请求的页面

request.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>请求页面</title>
</head>
<body>
<p><a href="some.do">发出一个请求</a></p>
</body>
</html>
```





### 创建控制器类

在controller包中创建一个**普通类**，名字随意。**在类上面加上@Controller注解，表明这是一个控制器对象。**

在其中创建一个方法，返回值是ModelAndView，它是Spring-web中的一个类，Model代表数据，View表示页面

没有参数。

**在方法上加上注解@RequestMapper，该注解有个value属性，字符串类型，是唯一的，**

**值是对应请求地址uri，推荐加上`/`，该注解把一个请求和一个方法映射起来。**

**也就是当发起这个请求时，DispatherServlet会把该请求转发给这个方法。**

**若有多个请求地址匹配该方法，则value值可以写上一个数组。**

加上注解@RequestMapper的方法叫做**控制器方法**，或者处理器方法。

```java
@Controller
public class MyController {

    @RequestMapping(value = "/some.do")
    public ModelAndView doSome() {
        ModelAndView mv = new ModelAndView();
        mv.addObject("param1", "开心");
        mv.addObject("param2", "快乐");
        mv.setViewName("/hello.jsp");
        return mv;
    }

}
```



### 创建欢迎页面

在WEB-INF/jsp目录中创建一个欢迎页面hello.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>欢迎页面</title>
</head>
<body>
<center>
    <h2>欢迎使用SringMVC做Web开发</h2>
    <h3>${param1}</h3>
    <h3>${param2}/h3>
</center>
</body>
</html>
```





### 创建配置文件

在resources目录中，创建springmvc.xml配置文件。和spring配置文件一样，可以直接使用spring配置文件模板。

在其中：

- 声明组件扫描器，用来扫描所有的注解，属性base-package指定@Controller注解所在的包名

- 声明视图扫描器，用来帮助处理视图。

    SpringMVC框架为了避免对于请求资源路径和拓展名的冗余，在视图解析器InternalResourceViewResolver中引入了请求的前缀和后缀，而ModelAndView只需要给出跳转页面的文件名即可。对于具体的文件路径和拓展名，视图解析器会自定完成拼接。

    语法格式为：

    ```xml
        <context:component-scan base-package="org.example.controller" />
        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="文件路径(文件所在目录)" />
            <property name="suffix" value="拓展名，以.开头" />
        </bean>
    ```

    这里使用：

    ```xml
        <context:component-scan base-package="org.example.controller" />
        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/WEB-INF/jsp" />
            <property name="suffix" value=".jsp" />
        </bean>
    ```

    那么在MyController的doSome方法中，`mv.setViewName("/WEB-INF/jsp/hello.jsp")`

    改成`mv.setViewName("hello")`即可。



### 注意

运行时出现了错误，出现了NoSuchMethodError，找不到对应方法。

找了各种各样的解决方法。最后找到了问题。是因为Tomcat版本和pom文件引入的Servlet依赖版本不一致导致的。

我的Tomcat服务器版本是8.5.59，对应的Servlet版本应该是`3.1.* `，对应jsp版本应该是2.3.*

下图是对应关系：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201208121209461.png" alt="image-20201208121209461" style="zoom:50%;" />



运行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201208121501667.png" alt="image-20201208121501667" style="zoom:50%;" />



## SpringMVC处理请求的流程

以上面的例子，来说明SpringMVC处理请求的流程

1. 用户发起some.do请求

2. tomcat服务器通过配置文件web.xml直到some.do的请求应该给DispatherServlet

3. DispatherServlet根据springmvc配置文件springmvc.xml知道要把请求转发给doSome()方法

4. doSome方法处理请求，返回ModelAndView对象

5. 框架把doSome返回的ModelAndView给视图解析器进行处理，视图解析器返回hello.jsp文件

    

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201208123400687.png" alt="image-20201208123400687" style="zoom:50%;" />





抽象出来，并更加细致的解释SpringMVC处理请求的流程

- 浏览器提交请求到中央调度器 

- 中央调度器直接将请求转给处理器映射器。

- 处理器映射器会根据请求，找到处理该请求的处理器，并将其封装为处理器执行链后 返回给中央调度器。 

- 中央调度器根据处理器执行链中的处理器，找到能够执行该处理器的处理器适配器。 

- 处理器适配器调用执行处理器。 

- 处理器将处理结果及要跳转的视图封装到一个对象 ModelAndView 中，并将其返回给 处理器适配器。 

- 处理器适配器直接将结果返回给中央调度器。 

- 中央调度器调用视图解析器，将 ModelAndView 中的视图名称封装为视图对象。

- 视图解析器将封装了的视图对象返回给中央调度器 

- 中央调度器调用视图对象，让其自己进行渲染，即进行数据填充，形成响应对象。 
- 中央调度器响应浏览器。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201208123559367.png" alt="image-20201208123559367" style="zoom:50%;" />









