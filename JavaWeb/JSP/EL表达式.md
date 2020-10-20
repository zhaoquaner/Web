EL表达式

## 什么是EL表达式

表达式语言(Expression Language, EL) ,EL表达式是用"${}" 括起来的脚本，用来更方便的读取对象。EL表达式主要用来读取数据，进行内容显示。

## 为什么使用EL 表达式

使用EL表达式，可以更简洁的读取对象中的属性，提交的参数、JavaBean、甚至集合。

例如在1.jsp中session设置了一个名为 name的属性

那么在2.jsp中间可以使用 `${name}`来读取该参数。



## EL表达式作用

首先EL表达式语法为：

${标识符}

**EL表达式如果找不到相应的对象属性，返回的是一个空白字符串"",而不是null，这是EL表达式最大的特点。**



### 获取各类数据

#### 获取域对象数据

在1.jsp中设置ServletContext属性(就是application)

```jsp
<%
//向ServletContext设置⼀个属性
application.setAttribute("name", "aaa");
System.out.println("向application设置了⼀个属性");
%>
```

在2.jsp中获取该属性

````jsp
<%
	${name}
%>
````



之前讲ServletContext对象时讲过一个方法findAttribute(String name)，EL表达式语句执行时会调用该方法，用标识符作为关键词分别从page、request、session、application四个域中查找相应对象。

查找顺序就是从小到大，上图所写顺序。





#### 获取JavaBean的属性

之前在JSP页面获取JavaBean属性需要先获取该对象，然后再访问该对象属性。使用EL表达式就很简单。

例如在1.jsp中定义了一个person对象，那么在2.jsp中直接使用`${person.age}`就可以访问person的age属性。



## 获取集合数据

EL表达式可以方便读取Collection和Map集合的内容。

例如，在1.jsp中设置session的属性，session属性的值为List集合，List集合装载Person对象

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" errorPage="error.jsp" %>
<html>
<head>
    <title>我是页头</title>
</head>
<body>

<%
    List<Person> list = new ArrayList<>();
    Person person1 = new Person();
    person1.setUserName("mike");
    person1.setAge(25);
    Person person2 = new Person();
    person2.setUserName("smith");
    person2.setAge(55);
    list.add(person1);
    list.add(person2);
    session.setAttribute("list", list);
%>
<jsp:forward page="2.jsp"/>>

</body>
</html>
```

在2.jsp中使用EL表达式，读取list集合内容

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>我是页尾</title>
</head>
<body>
    <%--取出person对象的属性--%>
对象person1的用户名为：${list[0].userName},年龄为：${list[0].age}<br>
对象person2的用户名为：${list[1].userName},年龄为：${list[1].age}
</body>
</html>
```



再使用Map集合

在1.jsp中session属性存储了Map集合，Map集合的关键词是字符串，值是Person对象

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" errorPage="error.jsp" %>
<html>
<head>
    <title>我是页头</title>
</head>
<body>

<%
    Map<String,Person> map = new HashMap<>();
    Person person1 = new Person();
    person1.setUserName("mike");
    person1.setAge(18);
    Person person2 = new Person();
    person2.setUserName("smith");
    person2.setAge(78);
    map.put("aa", person1);
    map.put("bb", person2);
    session.setAttribute("map", map);
%>
<jsp:forward page="2.jsp"/>>

</body>
</html>
```

在2.jsp中进行读取

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>我是页尾</title>
</head>
<body>
对象person1的用户名为：${map.aa.userName},年龄为：${map.aa.age}<br>
对象person2的用户名为：${map.bb.userName},年龄为：${map.bb.age}
</body>
</html>
```

如果Map集合存储键值不是字符串，而是一个数字，就不能使用"."号运算符了，可以这样：

```jsp
${map["1"].userName}
${map["2"].userName}
```



## EL运算符

EL表达式支持简单运算符：加减乘除取模，逻辑运算符，empty运算符(判断是否为null)和三目运算符

![image-20200908093738083](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/20200908100149.png)







## EL表达式11个内置对象

EL表达式主要用来对内容显示，为了显示方便，EL表达式提供了11个内置对象。

1. pageContext：对应JSP页面的pageContext对象
2. pageScope：代表page域中保存属性的Map对象
3. requestScope：代表request域中保存属性的Map对象
4. sessionScope：代表session域中保存属性的Map对象
5. applicationScope：代表application域中保存属性的Map对象
6. param：表示一个保存了所有请求参数的Map对象
7. paramValues表示了一个保存了所有请求参数的Map对象，它对于某个请求参数，返回的是一个string[]
8. header：表示一个保存了所有http请求头字段的Map对象
9. headValues：同上，返回string[]数组
10. cookie：表示一个保存了所有cookie的Map对象
11. initParam：表示一个保存了一个所有web应用初始化参数的map对象

注意事项：

- 测试headerValues时，如果头里面有“-”，例如Accept-Encoding，则要headValues["Accept-Encoding"]
- 测试cookie时，例如`${cookie.key}`取的是cooike对象，如果要访问cookie的名称和值，需要`${cookie.key.name}`或者${cookie.key.value}
- 测试initParam时，初始化参数要的web.xml中的配置Context的，仅仅是jsp的参数是获取不到的
- 对于param和paramValues内置对象一般都是别的页面带数据来的，如表单、地址栏。



## EL表达式回显数据

EL表达式最大特点就是如果获取到的数据为null，输出空白字符串"",这个特点可以让我们数据回显。

在1.jsp中模拟一下：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" errorPage="error.jsp" %>
<html>
<head>
    <title>我是页头</title>
</head>
<body>

<%
    Person person = new Person();
    person.setUserName("male");
    //回显数据
    request.setAttribute("person", person);
%>
<input type="radio" name="sex" value="male" ${person.userName=='male' ? 'checked' : ''}>女
<input type="radio" name="sex" value="female" ${person.userName=='female' ? 'checked' : ''}>男


</body>
</html>
```









