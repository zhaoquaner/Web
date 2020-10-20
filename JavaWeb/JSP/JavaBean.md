# JavaBean

## 什么是JavaBean

JavaBean就是普通的java类，也称为简单java对象，是Java程序设计中一种设计模式，是一种基于java平台的软件组件思想

JavaBean遵循特定写法，通常有如下规则：

- 有无参的构造函数
- 成员属性私有化
- 封装的属性如果需要被外所操作，必须编写public类型的setter，getter方法

JavaBean实际非常简单。下面就是按照规则编写的一个JavaBean对象：

```java
public class Person {
	private String username ;
	private int age;
	public Person() {
	}
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
}
```

## 为什么需要使用JavaBean

简单来说就是：封装，重写，可读！

引用一段回答：

 JaveBean你可以理解为⼀辆货⻋，在你的java端和web⻚⾯进⾏数据传递的载体，你当然可以每
个变量单独传递，或者使⽤集合传递，但是javabean可以使你的数据更有可读性，⽅便开发时明
确变量的意义，也使其他阅读你代码的⼈能直接你的意图。

如果把bean类和数据库联合使用，那么可以一张表使用一个bean类，使代码更加简洁高效，易于理解。现在大多数框架都会使用这种机制。



## JSP行为-JavaBean

JSP技术提供了三个关于JavaBean组件的动作元素，即JSP行为(标签)，分别为：

- jsp:useBean：在JSP页面中查找JavaBean对象或者实例化JavaBean对象
- jsp:setProperty：设置JavaBean属性
- jsp:getProperty：获取JavaBean属性

###　jsp:useBean

<jsp:useBean> 标签用于在指定域范围内查找指定名称的JavaBean对象

- 存在则直接返回该JavaBean对象的引用
- 不存在则实例化一个新的JavaBean对象并将它以指定名称存储到指定域范围中。

语法：

<jsp:useBean id="实例化对象的名称" class="类的全名" scope=“保存范围” />

如果JSP不支持这个行为，那么要使用Persion类是这样：

```jsp
<%--这⾥需要导⼊Person类--%>
<%@ page import="domain.Person" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
<title></title>
</head>
<body>
<%
//new出对象
Person person = new Person();
person.setName("zhongfucheng");
System.out.println(person.getName());
%>
</body>
</html>
```

我们使用jsp:useBean就非常简洁，不需要导包，不用new对象

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
<title></title>
</head>
<body>
<jsp:useBean id="person" class="domain.Person" scope="page"/>
<%
person.setName("zhongfucheng");
System.out.println(person.getName());
%>
</body>
</html>
```

要想使用这个行为，对应类必须有无参的构造函数，因为在内部中new对象时是没有传递参数的。



### jsp:setProperty

语法：

<jsp:setProerty name="对象名称" property="属性名" param="参数名" value="值">

语法上分为四种模式：

- <jsp:setProperty name="对象名称 "  property="*" /> ：自动匹配
- <jsp:setProperty name="对象名称 "  property="属性名称" />：指定属性
- <jsp:setProperty name="对象名称 "  property="属性名称" param="参数名称" />：指定参数(很少用)
- <jsp:setProperty name="对象名称 "  property="属性名称" param="参数名称"  value="内容"/>：指定内容(很少用)





例子：

例如表单代码为：

```html
<form action="/zhongfucheng/1.jsp" method="post">
⽤户名：<input type="text" name="username">
年龄：<input type="text " name="age">
<input type="submit" value="提交">
</form>
```

处理表单提交过来数据的jsp代码：

```jsp
<jsp:useBean id="person" class="domain.Person" scope="page"/>
<%
int age = Integer.parseInt(request.getParameter("age"));
person.setAge(age);
System.out.println(person.getAge());
%>
```

这样可以完成，表单提交过来的数据填充到person对象中，但是比较麻烦。

使用jsp:setProperty：

```jsp
<jsp:useBean id="person" class="domain.Person" scope="page"/>
<%--指定属性名称为age--%>
<jsp:setProperty name="person" property="age"/>
<%
System.out.println(person.getAge());
%>
```

也可以完成，并且代码更少。**无需去指定属性，它会去自动进行匹配。但是需要Persion的属性名称和表单的name属性名称是一模一样的。就可以自动完成匹配。**

再来一个：

```jsp
<jsp:useBean id="person" class="domain.Person" scope="page"/>
<%--property的值设置为*就代表⾃动匹配--%>
<jsp:setProperty name="person" property="*"/>
<%
System.out.println(person.getAge());
System.out.println(person.getName());
%>
```

只要在设置property的值为*，就可自动匹配。

我们不需要进行类型转换，内部会自动帮我们进行转换。





### jsp:getProperty

语法：

<jsp:getProperty name="对象名" property="属性名" />

该jsp行为很简单，直接使用该行为输出就可以

```jsp
<%--使⽤<jsp:getProperty>输出--%>
<jsp:getProperty name="person" property="username"/>
<jsp:getProperty name="person" property="age"/>
```

