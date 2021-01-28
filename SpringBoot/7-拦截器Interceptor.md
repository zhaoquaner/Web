# 7-拦截器Interceptor

在SpringMVC中使用拦截器方法是创建拦截器类实现HandlerInterceptor接口，并在springmvc配置文件中注册该拦截器。

而在SpringBoot中，使用方法稍微变动了一下。

1. 创建拦截器类并实现HandlerInterceptor接口，并编写相关代码
2. 在config包中创建配置类，并使用注解@Configuration来进行配置。配置类要实现WebMvcConfigurer接口

第一步不具体说了，详细说明在SpringMVC笔记中有。

重点说一下第二步。

## 配置类

在SpringMVC中，配置拦截器是在springmvc配置文件中设置的。那么在SpringBoot中没有配置文件。就是用注解来进行配置。

首先创建一个配置类，并实现WebMvcConfigurer。并在该类加上注解@Configuration

```java
@Configuration
public class InterceptorConfig  implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {

    }
}
```

然后在该类中重写WebMvcConfigurer接口的`addInterceptors`方法，该方法是用来注册拦截器类的。

该方法有一个形参，`InterceptorRegistry registry`，就是用来添加拦截器类的。

InterceptorRegistry类有三个方法：

- ` InterceptorRegistration addInterceptor(HandlerInterceptor)`：该方法用来添加拦截器对象，参数是一个拦截器对象，返回值是 InterceptorRegistration对象。
- `InterceptorRegistration addWebRequestInterceptor(WebRequestInterceptor interceptor)`：该方法用来添加继承了WebRequestInterceptor接口的拦截器对象，该接口和HandlerInterceptor的不同是：
    1. WebRequestInterceptor的形参WebRequest包装了HttpServletRequest和HttpServletResponse
    2. WebRequestInterceptor的preHandle方法没有返回值，说明该方法逻辑并不影响后续执行。所以该接口实现就是为了获取Request的信息。
    3. HandlerInterceptor的功能更加强大也更基础。
- `List<Object> getInterceptors()`：该方法用来获取所有已注册的拦截器。



其中InterceptorRegistration对象是用来进一步设置该拦截器的信息的，例如需要拦截的URL，不需要拦截的URL等。

该类所有的方法：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210123133210172.png" alt="image-20210123133210172" style="zoom:50%;" />

使用addPathPatterns方法来设置需要拦截的URL，使用excludePathPatterns方法来设置不需要拦截的URL。

形参是String数组或者List集合。



例如：

```java

@Configuration
public class InterceptorConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {

        String[] addPathPatterns = {
                "/spring/**","/springboot/**"
        };
        String[] excludePathPatterns = {
                "/spring/login","/springboot/login"
        };

        InterceptorRegistration interceptorRegistration =
                registry.addInterceptor(new MyInterceptor());
        interceptorRegistration.addPathPatterns(addPathPatterns);
        interceptorRegistration.excludePathPatterns(excludePathPatterns);
    }
}
```

这样就完成了拦截器的注册。





## 总结

@Configuration注解就相当于xml文件，重写方法相当于写具体的代码。



多个拦截器，在一个方法内使用同一个`InterceptorRegistry registry`对象多次添加即可。每添加一个拦截器对象，就返回一个InterceptorRegistration，通过该对象的`order(int order)`方法可设置拦截器执行顺序，数字越小优先级越高。

顺序只针对拦截器的preHandle方法。

对于postHandle方法和afterCompletion方法顺序刚好和preHandle方法是反着的。

因为每执行一个preHandle方法，就将对应的postHandle方法和afterCompletion方法放入相应栈中。所以执行的时候先放进去的后执行。



