# 2-SpringBoot核心配置文件

SpringBoot有两种核心配置文件格式，一种是`.properties`，另一种是`.yml`或`.yaml`格式。



**文件名必须是application。**

​	

默认采用`.properties`格式。

## .properties

这种配置文件格式 使用的是键值对组合，即key：value型。

例如：

```properties
#设置内嵌Tomcat端口号
server.port=80
#配置项目上下文根
server.servlet.context-path=/
```

通过这种方式来配置项目。

## .yml

yml是一种yaml格式的配置文件，采用**一定的空格、换行**等格式进行排版进行配置。

yaml是一种直观能被计算机识别的数据序列化格式，容易阅读，yaml类似xml，但是语法比xml简单。

**值和前面的冒号配置项之间必须有一个空格。**

yml后缀也可以改成yaml后缀。

例如：

```yml
server:
  port: 80 #80和前面的冒号中必须有一个空格
  servlet:
    context-path: /
```



当两种配置文件格式`.properties`和`.yml`同时存在，默认优先使用`.properties`格式文件。	



## 多环境配置文件

在实际开发中项目会经历很多阶段：开发、测试、上线。每个阶段采用的配置也不一样，例如端口、上下文等。

为了环境配置切换方便，SpringBoot提供了多环境配置。

具体实现：

1. 为每个环境提供一个配置文件，命名格式必须为：application-环境标识.yml/properties
2. 创建总的SpringBoot配置文件，applcation.yml/properties
3. 在总的配置文件中指明是使用哪个环境的配置文件

例如：

创建三个不同环境的配置文件：

`application-dev.properties`(开发环境)、`application-test.properties`(测试环境)、`application-product.properties`(生产环境)。

然后在总配置文件中，指定要使用的配置文件：

```properties
#SpringBoot的总配置文件

#激活开发环境
#spring.profiles.active=dev

#激活测试环境
#spring.profiles.active=test

#激活生产环境
spring.profiles.active=product
```

等号右边的值和配置文件中环境标识名称一致。可以修改总配置文件的配置，重新运行main方法。

现在使用的是生产环境的配置文件。



## 自定义配置

在核心配置文件中，除了内置的配置项外，还可以自定义配置，然后采用如下注解来读取

对应配置的属性值。

### @Value

该注解用于逐个读取配置文件中的配置。

例如：

在核心配置文件中加入自定义配置：

```properties
name=LiMing
address=America
```

然后在控制器中使用@Value注解将属性值注入变量中。

**使用`${自定义配置属性名}`来获取配置属性的值。**



控制器代码：

```java
@Controller
public class SpringBootController {

    @Value("${name}")
    private String name;

    @Value("${address}")
    private String address;

    @RequestMapping(value = "/springboot/sayHello")
    @ResponseBody
    public String sayHello() {
        return "我的名字是:" + name + ", 地址是:" + address;
    }
}
```

运行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210121160701430.png" alt="image-20210121160701430" style="zoom:50%;" />





### @ConfigurationProperties

该注解将整个文件映射成一个对象，用于自定义配置项比较多的情况。

在config包中创建ConfigInfo类，并给该类加上@Component和@ConfigurationProperties注解。

必须指定prefix属性，表示前缀。prefix用来区分同名配置项。



例子：
修改application.properties配置文件，加上自定义配置项：

```properties
student.name=Lily
student.address=America
```

有前缀student。

在config包中创建ConfigInfo类。

```java
@Component
@ConfigurationProperties(prefix = "student")
@Data
public class ConfigInfo {
    private String name;
    private String address;
}
```

指定属性prefix属性值为前缀student。

然后在控制器中使用创建ConfigInfo对象，并使用@Autowired注解自动注入。

```java
@Controller
public class SpringBootController {


    @Autowired
    private ConfigInfo configInfo;

    @RequestMapping(value = "/springboot/sayHello")
    @ResponseBody
    public String sayHello() {
        return "我的名字是:" + configInfo.getName() + ", 地址是:" + configInfo.getAddress();
    }
}
```

调用对象方法获取对应配置属性值。



注意：使用@ConfigurationProperties注解要加入SpringBoot注解处理器依赖，GAV坐标为：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```



另：在配置文件中，如果使用中文会出现乱码。一般也不建议在配置文件中出现中文。



