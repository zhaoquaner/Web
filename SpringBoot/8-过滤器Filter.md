# 8-过滤器Filter

SpringBoot使用过滤器有两种方式。一种是通过注解，另一种使用配置类。

## 注解

首先创建过滤器类，实现接口Filter。

使用**@WebFilter**注解，放在过滤器类上。该注解有一个参数urlPatterns，用来指定要过滤的请求url。

然后在启动类Application上加上注解**`@ServletComponentScan(basePackages = "包路径")`**

让SpringBoot扫描到过滤器类。

例子：

```java
@WebFilter()
public class MyFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		 chain.doFilter(request, response);
    }
}
```

在启动类加上注解：

```java
@ServletComponentScan(basePackages = "com.example.demo.filter")
@SpringBootApplication

public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

这样就可以使用过滤器了。







## 配置类

创建配置类并加上@Configuration注解来配置过滤器类。

```java
@Configuration
public class FilterConfig {

    @Bean
    FilterRegistrationBean filterRegistrationBean() {
        FilterRegistrationBean filterRegistrationBean
                = new FilterRegistrationBean(new MyFilter());
        filterRegistrationBean.addUrlPatterns("/*");
        return filterRegistrationBean;
    }
}
```

在配置类中创建一个方法，用来注册过滤器类，并通过@Bean注解交给Spring容器管理。

FilterRegistrationBean类用来实现注册过滤器类。创建该对象，将Filter对象作为参数传给该对象构造器，并调用`addUrlPatterns()`方法设置该过滤器的拦截请求URL。



通过该对象的`setOrder(int order)`方法来设置过滤器的优先顺序。

参数是int类型，数字越小优先级越高。可以从数字1开始。

也可以使用@Order(value="")注解放到该方法上，来指定优先级。





## **注意**

- **如果要设置优先级，则只能使用配置类方法来配置过滤器。注解方式无法设置优先级。(哪怕在过滤器类上加上@Order注解，也不起作用)。**

- **注册几个过滤器就需要写几个方法，分别加上@Bean注解。**

- **千万别忘了写`chain.doFilter(request, response);`**







