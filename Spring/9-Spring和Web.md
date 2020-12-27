# 9-Spring和Web

​		在Web项目中使用Spring框架，首先要在web层(这里指Servlet)获取到Spring容器对象。只要获取到了Spring容器，就可以从该容器中获取到Service对象。

​		那么接下来就要思考在哪里创建Spring容器。

​		对于一个web应用来说，Spring容器对象只需要一个就可以了。所以很显然不能直接在Servlet中创建Spring容器，因为虽然Servlet是单例多线程，但多个Servlet都需要用到Spring容器，就会创建多个Spring容器。

​		我们想到，对于一个web应用，全局作用域对象ServletContext也只有一个，所以可以在创建全局作用域对象的时候创建Spring容器，并把Spring容器对象存入ServletContext中。

​		之前学到了全局作用域的监听器接口ServletContextListener，我们可以使用监听器监听全局作用域，当全局作用域创建的时候同时创建Spring容器，并放入全局作用域对象中。

​		可以自定义全局作用域监听器接口实现类，也可以使用spring的一个模块spring-web提供的实现类ContextLoaderListener。



## 使用步骤

### 加入依赖

首先加入spring-web依赖

```xml
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>5.3.1</version>
    </dependency>
```

### 在web配置文件web.xml中注册监听器

```xml
    <listener-class>
      org.springframework.web.context.ContextLoader
    </listener-class>
  </listener>
```



### 在web配置文件web.xml文件中指定spring配置文件路径

使用<context-param>标签来配置，语法格式为：

```xml
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring配置文件路径</param-value>
  </context-param>
```

子标签<param-name>的值就是contextConfigLocation，固定不变。<param-value>就是spring配置文件的路径。

ContextLoaderListener会从ServletContext中获取该参数，然后创建spring容器。

注意，如果从ServletContext中获取web.xml初始参数，需要使用的方法是`getInitParameter(String name)`，而不是`getAttribute(String name)`。



如果没有使用<context-param>标签指定spring配置文件路径，那么会默认使用WEB-INF/applicationContext.xml这个路径。



#### 获取Spring容器对象

获取Spring容器对象有两种方法。

在之前的例子中，Spring容器对应类时ApplicationContext，在web应用中容器对应类时WebApplicationContext，它是ApplicationContext的子类。



#### 从ServletContext中获取

Spring容器对象在ServletContext中存放的key为`WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE。`

可以通过这个key，调用ServletContext的`getAttribute(String name)`方法获取WebApplicationContext。

```java
String attr = "WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE";
WebApplicationContext ac = (WebApplicationContext) this.getServletContext().getAttribute(attr);
```



#### 通过WebApplicationContextUtils获取

WebApplicationContextUtils是一个工具类，它有一个`getRequiredWebApplicationContext(ServletContext sc)`方法，传入ServletContext参数，返回值就是Spring容器对象。

```java
WebApplicationContext wac = WebApplicationContextUtils.getRequiredWebApplicationContext(sc);
```





