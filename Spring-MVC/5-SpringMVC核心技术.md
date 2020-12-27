# 5-SpringMVC核心技术

## 请求转发和重定向

当处理器对请求处理完毕后，向其他资源跳转时，有两种跳转方式：请求转发和重定向。

**请求转发，是在服务器内进行跳转，不经过客户端，浏览器地址栏是不变的；而重定向是告诉客户端下一个访问的地址，让客户端重新发起一次请求，浏览器地址会改变。**

而根据所要跳转的资源类型，又分为两类：跳转到页面、跳转到其他处理器方法。

**但是注意：对于请求转发的页面，可以是WEB-INF中页面；但是重定向页面，是不能为WEB-INF中页面的。因为重定向相当于用于重新发起一次请求，而用户是不能直接访问WEB-IN目录的。**



SpringMVC框架对Serlvet中的请求转发和重定向操作进行了封装：

- forward：表示转发，等于`request.getRequestDispather("xx").forward(request, response)`
- redirect：表示重定向，相当于`response.sendRedirect("xxx")`



### 请求转发

#### 处理器方法返回ModelAndView

返回ModelAndView时，要在`setViewName("视图路径")`前加上forward，即：`setViewName("forward:视图完整路径")`

**注意：使用请求转发到视图时，必须要写完整路径，即以resources为根目录；并且不和视图解析器一同使用，使用请求转发时，就当没有视图解析器。**



例如：`mv.setViewName("forward:/WEB-INF/result.jsp");`

#### 返回String

和返回ModelAndView一样，字符串格式：`"forward:视图完整路径"`



### 重定向

重定向使用方式和请求转发差不多，格式`"redirect:视图完整路径"`。同样不和视图解析器一同使用。

**但是重定向不能访问WEB-INF目录下资源。**



**注意：如果在一个页面传递参数给服务器，服务器进行重定向，并将相应参数通过mv.addObject()传递给jsp文件，然后重定向的jsp调用了这个参数，那么是获取不到的。**



举个例子：
注册页面，传递参数给服务器，然后处理器方法将参数使用`addObject()`添加到ModelAndView中，并重定向到result.jsp中，jsp文件调用了这个参数。

注册页面代码：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>注册页面</title>
    <base href="http://localhost:80/myweb/">
</head>
<body>
    <div align="center">
        <form action="student/addStudent.do" method="post">
            <table>
                <tr>
                    <td>姓名</td>
                    <td><input type="text" name="name"></td>
                </tr>
                <tr>
                    <td>Email</td>
                    <td><input type="text" name="email"></td>
                </tr>
                <tr>
                    <td>年龄</td>
                    <td><input type="text" name="age"></td>
                </tr>
                <tr>
                    <td><input type="submit" value="注册"></td>
                </tr>
            </table>
        </form>
    </div>
</body>
</html>
```

处理器方法：

​	

```java
@RequestMapping(value = "/addStudent.do")
public ModelAndView addStudent(Student student) {
    ModelAndView mv = new ModelAndView();
    String tip;
    int num = service.addStuent(student);

    mv.addObject("myname", student.getName());
    mv.addObject("myage", student.getAge());
    mv.setViewName("redirect:/views/result.jsp");
    return mv;
}
```



result.jsp：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <div align="center">
        姓名:${myname}<br>
        年龄:${myage}
    </div>
    
</body>
</html>
```

运行结果：


![FfWQyjZeRl](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/FfWQyjZeRl.gif)



可以看到，在result.jsp页面获取不到处理器方法传递的数据。但是地址栏发生了改变而且带上了处理器方法放入到ModelAndView的两个参数myname和myage。



**这是因为：框架会把Model中的简单类型的数据，转为String，作为重定向页面的get请求参数使用，目的是在这两次请求之间传递参数。**

但result.jsp使用${}获取不到数据，是因为，重定向是两次请求，用了两个request对象，所以获取不到。那么正确的用法应该是result.jsp从URL中获取参数，也就是`request.getParameter("myname")`。才可以获取到参数。

把result.jsp改为：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <div align="center">
        姓名:<%=request.getParameter("myname")%><br>
        年龄:<%=request.getParameter("myage")%>
    </div>
</body>
</html>
```

或者也可以使用`${param.myname}`和`${param.myage}`

运行结果：

![q9xo4jcpGj](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/q9xo4jcpGj.gif)



这次可以看到，result.jsp正确获取到了数据。



以上面为例，来梳理一下重定向处理过程 ：

首先点击注册，客户端发送请求`/student/addStudent.do`，**这时会创建一个request对象，包含参数**；然后处理器方法添加myname和myage两个参数到ModelAndView中，然后执行重定向这行，最后服务器会把mv对象返回给客户端，客户端收到重定向指令后，**会再次携带ModelAndView存放的参数，向ModelAndView指定的页面result.jsp发送get请求，同时参数也会出现在地址栏中。服务器收到后，会再次创建一个request对象，所以这时在result.jsp中使用`${myname}`是获取不到数据的。只有使用`request.getParameter()`获取客户端发送来的参数才可以。**









## 异常处理

通常处理异常的方式就是try/catch代码块。但是当代码一多，方法一多了以后，这种方法就非常繁琐。而且也不便于维护。

SpringMVC框架处理异常的方式是：使用@ExceptionHandler注解和@ControllerAdvice注解处理异常。

SpringMVC框架采用的是统一的、全局的异常处理。把Controller中的所有异常处理集中到一个地方，采用AOP思想，把业务逻辑代码和异常处理代码分开，解耦合。

### @ExceptionHandler

使用@ExceptionHandler可以将一个方法指定为异常处理方法，该注解只有一个可选属性

- value：他是一个Class<?>数组，用于指定该注解的方法要处理的异常类。就是当发生哪些异常，使用该方法处理。默认为空数组

被注解的方法，返回值可以是ModelAndView、String、void和对象等，方法名任意。

方法参数可以是Exception和子类对象，HttpServletRequest、HTTPServletResponse等。系统会自动给这些参数赋值。



### @ControllerAdvice

这个注解字面意思是：控制器增强，也就是给控制器增加功能。

它放在类的上面，表明这是一个异常处理类。

使用@ControllerAdvice的类可以创建异常处理方法，并在方法上加上@ExceptionHandler。

说简单点，就是被@ExceptionHandler注解的类，就表明是一个专门处理controller方法异常的类。

这个类中的方法来处理异常。

**注意：需要在springMVC配置文件中配置组件扫描器，指定@ControllerAdvice所在包名。**



### 异常处理步骤

1. 在exception包中创建自定义异常类

2. 在handler包中创建异常处理类，并加上@ControllerAdvice注解

    并创建处理异常方法，加上@ExceptionHandler注解

3. 创建处理异常页面视图

4. 在springMVC中配置@ControllerAdvice注解所在包名，并声明注解驱动

5. 在处理器方法中抛出异常

**注意：当异常处理类中有多个方法时，那就和catch差不多。我们一般只会处理几个特定的类，将其他的所有异常抛给虚拟机。异常处理类也是这样。 使用@ExceptionHandler中value属性指定我们要处理的异常类；不指定value属性代表其他所有异常。所以没有指定value属性的被注解方法只能有一个。**



异常发生处理一般有几个方面：

- 记录异常，包括记录到数据库，日志文件，日志发生的时间，错误内容等
- 发送通知给相关人员
- 给用户提示



### 一个例子

学生注册，当姓名或者年龄不符合要求就抛出异常。



#### 创建异常类

创建三个异常类：UserException、NameException、AgeException。后两个类是UserException的子类

这三个方法实现无参构造器和有参数message构造器。

### 创建异常处理类

GlobalExceptionHandler类

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(value = {NameException.class})
    public ModelAndView doNameException(Exception ex) {
        ModelAndView mv = new ModelAndView();
        mv.addObject("msg", ex.getMessage());
        mv.setViewName("nameError");
        return mv;
    }

    @ExceptionHandler(value = {AgeException.class})
    public ModelAndView doAgeException(Exception ex) {
        ModelAndView mv = new ModelAndView();
        mv.addObject("msg", ex.getMessage());
        mv.setViewName("ageError");
        return mv;
    }

    @ExceptionHandler
    public ModelAndView doDefaultException(Exception ex) {
        ModelAndView mv = new ModelAndView();
        mv.addObject("msg", ex.getMessage());
        mv.setViewName("defaultError");
        return mv;
    }

}
```



三个方法分别处理NameException、AgeException和其他异常。



#### 创建异常页面

对应有三个页面：nameError、ageError和defaultError、

nameError：

```jsp
<body>
    <div align="center">
        nameError<br>
        ${msg}
    </div>
</body>
```

ageError：

```jsp
<body>
    <div align="center">
        ageError<br>
        ${msg}
    </div>
</body>
```

defaltuError：

```jsp
<body>
    <div align="center">
        defaultError<br>
        ${msg}
    </div>
</body>
```

#### 修改springmvc配置文件

在配置文件中加上对handler的注解扫描器，和注解驱动。

```xml
<context:component-scan base-package="org.example.handler" />
<mvc:annotation-driven/>
```

#### 处理器方法

```java
@RequestMapping(value = "/addStudent.do")
public ModelAndView addStudent(Student student) throws UserException {
    ModelAndView mv = new ModelAndView();
    if(!student.getName().equals("大傻子")){
        throw new NameException("姓名只能为大傻子，其他姓名不行");
    }
    if(student.getAge() > 80) {
        throw new AgeException("年龄要小于80岁");
    }

    int num = service.addStuent(student);

    mv.addObject("myname", student.getName());
    mv.addObject("myage", student.getAge());
    mv.setViewName("redirect:/views/result.jsp");
    return mv;
}
```



运行结果：

对应三个异常，分别进行验证。

输入名称二傻子验证NameException，输入年龄90验证ageException，输入年龄abc验证其他异常。

![DXq8izZuEb](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/DXq8izZuEb.gif)





## 拦截器

SpringMVC拦截器的主要作用是拦截指定的用户请求，并进行响应的预处理和后处理。拦截的时间点在：**处理器映射器根据用户提供的请求映射出了所要执行的处理器类，并且找到了要执行该处理器类的处理器适配器，在处理器适配器执行处理器之前。进行拦截。**当然，在处理器映射器映射出所要执行的处理器类时，已经将拦截器和处理器组合为一个处理器执行链，并返回给了中央调度器。



### 自定义拦截器

实现HandlerInterceptor接口，来自定义拦截器类，该接口有三个方法：

- `public boolean preHandle(request,response, Object handler)`：该方法在处理器方法执行之前执行，返回值为Boolean，若为true，则执行处理器方法，并将afterCompletion()方法放入到专门方法栈中等待执行；若为false，则不执行处理器方法
- `public boolean postHandle(request,response, Object handler,modelAndView)`：该方法在处理器方法执行之后执行，若处理器方法不执行，那么该方法不会执行。由于该方法在处理器方法执行后执行，且方法参数中包含ModelAndView，所以该方法可以修改处理器方法的处理结果数据，也可以修改跳转方向。
- `afterCompletion(request, response, Object handler, Exception ex)`：当preHandler()方法返回true时，会将该方法放到专门方法栈中，等到对请求进行响应的所有工作完成之后，才执行该方法。**即：该方法是在中央调度器渲染(数据填充)了相应页面后执行的，所有该方法对ModelAndView操作对不影响响应结果。**



处理过程：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201222195256643.png" alt="image-20201222195256643" style="zoom:80%;" />



### 拦截器使用步骤

1. 定义拦截器类
2. 在springmvc配置文件中注册拦截器，并设置要拦截的请求。



例子：

定义拦截器类：

```java
public class MyInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("在处理器方法之前执行了preHandle方法");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("在处理器方法之后执行了postHandle方法");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("执行了afterCompletion方法");
        HttpSession session = request.getSession();
        Object attr = session.getAttribute("attr");
        System.out.println("attr删除之前====" + session.getAttribute("attr"));
        session.removeAttribute("attr");
        System.out.println("attr删除之后====" + session.getAttribute("attr"));

    }
}
```



处理器方法：

```java
@RequestMapping(value = "/listStudents.do")
@ResponseBody
public List<Student> listStudents(HttpSession session) {
    System.out.println("收到请求");
    List<Student> list = service.selectStudents();
    session.setAttribute("attr", "session中数据");
    return list;
}
```



springmvc配置文件中注册拦截器：

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/student/listStudents.do"/>
        <bean class="org.example.interceptor.MyInterceptor" />
    </mvc:interceptor>
</mvc:interceptors>
```

运行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201222201818458.png" alt="image-20201222201818458" style="zoom:80%;" />



### 多个拦截器

当有多个拦截器时，在springmvc配置文件中就要注册多个拦截器。

当有多个拦截器时，**形成拦截器链，拦截器链的执行顺序和在配置文件中的注册顺序一样。**

**注意：当某一个拦截器的preHandle()方法返回true并被执行时，会向专门的方法栈中放入该拦截器的afterCompletion。**



只要有一个preHandle()方法返回false，那么执行链会被断开，后续的处理器方法和postHandle()方法会无法执行。

**但是，无论执行链执行情况怎样，只要方法栈中有方法，即执行链中有preHandle方法返回true且被执行，那么就会执行方法栈中的afterCompletion方法。**

也就是只执行执行链断开前 执行过的preHandle方法对应的afterCompletion方法。



例子：

创建了三个拦截器，大致相同，第一个拦截器代码：

```java
public class MyInterceptor1 implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("在处理器方法之前执行了第一个拦截器的preHandle方法");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("在处理器方法之后执行了第一个拦截器的postHandle方法");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("执行了第一个拦截器的afterCompletion方法");

    }
}

```

其他两个，把输出语句改成对应序号就可以了。**把第二个拦截器的preHandle返回值改为false。**

控制器方法：

```java
    @RequestMapping(value = "/listStudents.do")
    @ResponseBody
    public List<Student> listStudents(HttpSession session) {
        System.out.println("收到请求");
        List<Student> list = service.selectStudents();
        return list;
    }
```

springmvc配置文件注册拦截器：

```xml
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/student/listStudents.do"/>
            <bean class="org.example.interceptor.MyInterceptor1" />
        </mvc:interceptor>
    </mvc:interceptors>
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/student/listStudents.do"/>
            <bean class="org.example.interceptor.MyInterceptor2"/>
        </mvc:interceptor>
    </mvc:interceptors>
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/student/listStudents.do"/>
            <bean class="org.example.interceptor.MyInterceptor3" />
        </mvc:interceptor>
    </mvc:interceptors>
```

注册顺序是123

执行结果：
	<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201226151415085.png" alt="image-20201226151415085" style="zoom:80%;" />



可以看到，执行链在执行到第二个拦截器的preHandle后断开，那么只有第一个拦截器的afterCompletion被执行了。



### 权限拦截器

可以使用拦截器来验证权限，没有权限的不能访问。

只有先点击登录，才后台添加session，才可以查看学生信息，否则无法查看学生信息。

控制器代码：

```java
    @RequestMapping(value = "/listStudents.do")
    @ResponseBody
    public List<Student> listStudents() {
        System.out.println("收到请求");
        List<Student> list = service.selectStudents();
        return list;
    }

    @RequestMapping(value = "/login.do")
    public String login(HttpSession session){
        session.setAttribute("attr", "success");
        return "index";
    }
```

index.jsp

```jsp
<html>
<head>
    <base href="<%=basePath%>views/">
</head>
<body>
<h2>SSM整合开发</h2>
<a href="register.jsp">学生注册</a><br>
<a href="listStudents.jsp">查看学生信息</a>
<a href="/myweb/student/login.do">登录</a>
</body>
</html>
```

拦截器代码：

```java
public class MyInterceptor1 implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        Object attr = session.getAttribute("attr");
        if(attr == null) {
            return false;
        }
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("在处理器方法之后执行了postHandle方法");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("执行了afterCompletion方法");

    }
}
```



springmvc配置拦截器：

```
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/student/listStudents.do"/>
        <bean class="org.example.interceptor.MyInterceptor1" />
    </mvc:interceptor>
</mvc:interceptors>
```

执行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/Olxo61hNGB.gif" alt="Olxo61hNGB" style="zoom:80%;" />





首先点击查看学生信息，可以看到，没有任何信息；点击登录，再查看信息，有了。