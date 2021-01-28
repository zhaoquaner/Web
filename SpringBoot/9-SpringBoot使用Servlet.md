# 9-SpringBoot使用Servlet

在SpringBoot中有两种方法可以直接使用Servlet。当然一般都直接使用控制器类。



## 注解

使用注解**@WebServlet**来标识该类是一个Servlet类可以被SpringBoot扫描到。

该注解有一个属性urlPatterns来表示该Servlet处理哪个或者哪些请求。

然后在主类Application上加上注解**@ServletComponentScan("包路径")**，使用SpringBoot能够扫描到该Servlet。

例子：
创建一个Servlet类，继承HttpServlet。

```java
@WebServlet("/springboot/sayHello")
public class MyServlet extends HttpServlet {
    private static final long serialVersionUID = -5556813722982445881L;

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        PrintWriter pw = resp.getWriter();
        pw.println("Hello World!");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        PrintWriter pw = resp.getWriter();
        pw.println("Hello World!");
    }
}
```

然后在主类上加上注解**@ServletComponentScan("包路径")**。

```java
@ServletComponentScan(basePackages = "com.example.demo.controller")
@SpringBootApplication

public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

就可以了。



## 配置类

第二种方式是使用配置类。

首先创建一个关于注册Servlet的配置类。并在该类上加上注解@Configuration

并创建一个方法：

```java
@Configuration
public class ServletConfig {

    @Bean
    public ServletRegistrationBean httpServletRegistrationBean() {
        
        ServletRegistrationBean servletRegistrationBean
                = new ServletRegistrationBean(new MyServlet(),"/springboot/sayHello");
        return servletRegistrationBean;

    }
}
```

**在方法上加上注解@Bean。该注解表示将该方法交给Spring容器管理。即该方法会产生一个对象，Spring容器将管理该对象。**

**@Bean注解只能在@Configuration注解下使用。**

**SerlvetRegistrationBean表示一个Servlet注册类对象。**

**在该方法中创建该对象，并将Servlet对象和该Servlet对象要处理的请求url作为参数。最后返回该对象。**



当然直接使用Servlet很少用，一般直接用控制器。



​	