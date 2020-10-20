# 1-JSP入门

JSP全称为Java Server Pages，java服务器页面，特点是HTML代码和Java代码共同存在。



## 为什么需要JSP

Servlet输出HTML比较麻烦，代码量比较多，很不方便，JSP就是替代Servlet输出HTML的。



## JSP工作原理

Tomcat访问任何资源其实都是在访问Servlet，JSP也不例外。JSP本身就是一种Servlet，为什么呢？其实JSP在第一次被访问时会被编译成HttpJspPages类，是HttpServlet的一个子类。

编译过程是：浏览器第一次请求jsp文件时，Tomcat会将jsp文件转换为java一个类，然后编译为class文件，然后再运行class文件来响应浏览器请求。

以后在访问该jsp文件就不会重新编译了，直接调用class文件，如果tomcat检测出jsp页面改动的话，就会重新编译。



JSP是一个Servlet，那么jsp页面中html是如何发送到浏览器的呢》其实就是使用write方法发送的。所以JSP就是封装了Servlet的java程序而已。

那么JSP的代码服务器是怎么执行的呢？HttpJspPages类中有一个方法叫_jspService()，由jsp转换的java代码就在该方法中。



JSP之所以比Servlet更方便简单的一个原因是：JSP内置了9个对象：out、session、response、request、config、page、application、pageContext、exception。



## JSP生命周期

JSP也是一个Servlet，运行时也只有一个实例，JSP初始化和销毁时会调用Servlet的init和destroy方法。另外，JSP还有自己初始化和销毁的方法。

![image-20200821142735578](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/20200821142742.png)



## JSP基本语法

JSP代码分为两部分：

- HTML代码
- 元素：JSP页面中的java代码、JSP指令、JSP标签

### JSP脚本

JSP脚本就是java代码，也叫做scriptlet。JSP的脚本必须使用<%%>括起来，不然JSP文件无法识别。

JSP脚本有三种方式：

- <%%>：定义局部变量，编写语句
- <%!%>：定义类或方法，**但是没有人这样用**
- <%=%>：表达式输出输出各种类型变量，例如<%=num1%>，就是输出num1变量

### JSP指令

JSP指令用来声明JSP页面相关属性，例如编码方式，文档类型等。

JSP指令的语法格式为：

<%@ 指令 属性="值" %>

 

例如，page指令常见的属性：

- language="java"
- extends="package.class"
- import="{package.class | package.*},..."
- session="true | false"
- buffer="none | 8kb | size kb"
- autoFlush="true | false"
- info="text"
- errorPage="relatice_url"
- isErrorPage="true ｜false"
- contentPage="mineType; charset=charracterSet" | "text/html";charset=ISO-8859-1"
- pageEncoding="characterSet  | ISO-8859-1"
- isELIgnored="true | false"



一般在高级开发工具上例如IDEA开发，只需要在page指令中指定contentType="text/html;charset=utf-8",就不会出现中文乱码问题。当然contentType不仅可以指定以text/html方式显示出来，还可以使用其他形式。

使用errorPage属性可以指定如果当前页面出错，要跳转到哪个页面，isErrorPage指定该页面是否为要跳转的错误页面。例如：

```jsp
<!--index.jsp代码-->
<%@ page contentType="text/html;charset=UTF-8" language="java" errorPage="error.jsp" %><!--指定出错后，跳转到error页面-->
<%
    int result = 2 / 0;//这是出错位置
%>
变量num3的值为：<%=result%><br>

<!--error.jsp代码-->
<%@ page contentType="text/html;charset=UTF-8" language="java" isErrorPage="true" %><!--指定该页面为错误页面-->
<html>
<head>
    <title>Title</title>
</head>
<body>
    <center>
        <font style="color: red; font-size: 40px">您访问的页面出错啦出错啦～～～</font>
    </center>

</body>
</html>
```

执行结果为：

![image-20200821150012375](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/20200821150012.png)

但是发现，地址栏并没有改变，说明这次跳转是在服务器内发生的。

但是，这样一个一个jsp去设置错误页面太麻烦了，我们可以在web.xml文件中去指定全局错误页，无论哪个jsp发生错误，都会转到该页。

代码为：

```jsp
<error-page><!--这个错误页面标签指定当发生404错误代码时，跳转到error.jsp页面-->
<error-code>404</error-code>
<location>/error.jsp</location>
</error-page>

<error-page><!--这个错误页面标签指定当发生空指针异常时，跳转到error.jsp页面-->
<exception-type>java.lang.NullPointerException</exception-type>
<location>/error.jsp</location>
</error-page>
```



#### 将HTML代码和Java代码混合使用

例如：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
    int age = 11;
%>

<%
    if(age >= 15) {
%>
<font style="color:red; font-size: 40px">欢迎光临</font>
<%
    } else {
%>
<font style="color:red; font-size: 40px">谢绝入内</font>
<%
    }
%>
```

多个<%%>可以看成是一个<%%>标签，使用if else语句来进行选择输出。

再例如使用for循环：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
    String s = "afgdhshsg";
%>

<%
    for(int i = 0; i < s.length(); i++) {
%>
    <h1><%=s.charAt(i)%></h1>
<%
    }
%>
```



#### include指令

在学request对象时，曾经使用request.getRequestDispatcher(String url).include(request, response)来对页头和页尾进行包括。

include指令也是相同的作用。

include指令是静态包含，意思就是：比文件的代码内容都包含进来，再进行编译。

例如：

1.jsp文件和2.jsp文件分别为：

```jsp
<!--1.jsp-->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>我是页头</title>
</head>
<body>
我是页头
</body>
</html>

<!--2.jsp-->
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>我是页尾</title>
</head>
<body>
我是页尾
</body>
</html>
```

然后index.jsp为：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
    <body>
        <%@include file="1.jsp"%>
        <%@include file="2.jsp"%>
    </body>
</html>
```

输出内容为前两个文件的内容。

#### taglib指令

JSP支持标签技术，要使用标签技术就要先声明标签库和标签前缀，taglib指令就是用来指明JSP页面内使用标签库技术。

后面再细说。



###　JSP行为

JSP行为是一组内置的标签，只需要书写少量标记代码就能够使用JSP提供的丰富功能。JSP行为是对常用的JSP功能的抽象和封装。

这些其实就是标签。但之所以把这些内置标签称为行为，是因为要把JSTL标签区分开。当然，也可以直接把它称之为JSP标签。

#### include行为

刚才说了，include指令是静态包含，include行为是动态包含。include行为就是封装了request.getRequestDispatcher(String url).include(request, response)

include行为语法格式为：<jsp:include page="" />

现在有静态包含和动态包含，但是推荐使用动态包含。

原因是：动态包含可以向被包含的页面传递参数（但是用处不大），并且分别处理包含页面(就是先将包含页面编译再写入)，如果有相同名称的参数，使用静态包含就会报错(因为静态包含是先写入再编译)，使用动态包含就不会出错。

#### param行为

当使用jsp:include和jsp:forward行为引入或请求转发其他资源时，可以使用jsp:param行为向这个资源传递参数

#### forward行为

学习request对象时，使用request.getRequestDispatcher(String url).forward(request, response)进行跳转，forward行为就是对其进行封装。

语法为：

<jsp:forward page="" />



如果要传递参数，就需要在forward行为嵌套param行为

```jsp
<jsp:forward page="2.jsp">
    <jsp:param name="username" value="hhhh" />
</jsp:forward>    
```



在2.jsp页面中获取到传递的参数

<%

​		String ss = request.getParameter("username");

%>





















