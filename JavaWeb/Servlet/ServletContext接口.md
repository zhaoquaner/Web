### ServletContext接口

当Tomcat启动时，就会创建一个ServletContext对象，它代表着当前web站点，该网站所有Servlet共享一个ServletContext对象，所有Servlet对象之间可以通过ServletContext实现通讯。

ServletContext对象被称为域对象，或者全局作用域对象，可以把它理解为一个容器，我们把需要共享的数据放到这个容器中，需要对应数据的Servlet对象就从这个容器中拿。

​			实现Servlet通讯要用ServletContext的`setAttribute(String name, Object obj)`方法，第一个参数为变量名，第二个参数为变量值；然后使用`servletContext.getAttribute(String name)`获取指定变量值，需要强制类型转换，例如：

```java
//第一个Servlet代码
ServletContext servletContext = this.getServletContext();
String value = "HAHAHAH";
servletContext.setAttribute("Name", value);

//第二个Servlet代码
ServletContext servletContext = this.getServletContext();
String value = servletContext = (String)servletContext.getAttribute("name");
```



		#### 获取web站点配置的信息

如果想要让所有Servlet都能够获取连接数据库的信息，不可能在web.xml给每个Servlet都配置，太麻烦，web.xml文件支持对整个站点进行配置参数信息，所有的Servlet都可以拿到该配置信息。

例如：
web.xml的内容：

```xml
<context-param>
    <param-name>name</param-name>
    <param-value>HAHAHA</param-value>
</context-param>
```

Servlet的代码：

```java
ServletContext servletContext = this.getServletContext();
String value = servletContext.getInitParameter("name");
System.out.println(value);
```

获取web.xml配置参数时，要使用getInitParameter(String name)方法。

#### ServletContext的生命周期

- 当Tomcat启动过程中，自动为当前网站在内存中创建一个ServletContext对象
- 在Http服务器运行期间，一个网站只有一个ServletContext对象，并且一直处于存活状态
- 当Http服务器准备关闭时，负责将当前网站中ServletContext对象进行销毁



