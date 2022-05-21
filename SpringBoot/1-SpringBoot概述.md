# 1-SpringBoot概述

SpringBoot的设计目的是**简化**Spring应用的初始搭建和开发过程。该框架使用了特定的方式来进行配置，从而使开发人员不需要定义样板化的配置。

在以往采用SpringMVC+Spring+MyBatis开发时，搭建和整合三大框架，需要做很多工作，配置web.xml。配置Spring和MyBatis。而SpringBoot框架对此开发过程进行了革命性颠覆，完全抛弃了繁琐的xml配置过程，采用大量默认配置来简化开发过程。

​		

## SpringBoot特性

SpringBoot有这些特性：

1. 能够快速创建基于Spring的应用程序
2. 能够直接使用java的main方法启动内嵌的Tomcat服务器运行SpringBoot程序，不用部署war包
3. 提供约定的starter POM来简化Maven配置，使Maven配置更简单
4. 自动化配置，根据项目Maven依赖配置，SpringBoot自动配置Spring、SpringMVC。
5. 提供了程序的健康检查功能
6. 基本不使用XML配置文件，采用注解配置



SpringBoot有四大核心：

1. 自动配置：针对很多Spring常见的功能，SpringBoot能自动提供相关配置
2. 起步依赖，在创建SpringBoot项目时，告诉SpringBoot需要什么功能， 它就会自动引入相关依赖库
3. Actuator：能够深入运行中的SpringBoot应用程序，一探SpringBoot程序内部信息
4. 命令行界面：这是SpringBoot的可选特性，主要针对Groovy语言使用。







## 第一个SpringBoot项目



使用IDEA来创建SpringBoot项目，使用Spring Initializr来创建SpringBoot项目。可以使用默认的`https://start.spring.io`，也可以使用阿里提供的`http://start.aliyun.com`。

jdk版本要大于8。

点击Next：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210121142542978.png" alt="image-20210121142542978" style="zoom:50%;" />

其中：

- Group和Artifact和Maven配置一样。
- Packaging是用来设定打包方式，默认使用jar
- Package指定SpringBoot启动类所在的包路径

点击Next：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210121143108611.png" alt="image-20210121143108611" style="zoom:50%;" />

在此处添加所需要的依赖。

然后下一步来设置模块名称，Content root的根路径和模块文件的根路径。就创建成功了。

SpringBoot的pom文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    
    <!--继承SpringBoot框架的一个父项目，所有自己开发的Spring Boot都必须的继承-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.5.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>10-SpringBootDemo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>10-SpringBootDemo</name>
    <description>Demo project for Spring Boot</description>

    <!--maven属性配置，可以在其它地方通过${}方式进行引用-->
    <properties>
        <java.version>1.8</java.version>
    </properties>


    <dependencies>
        <!--SpringBoot框架web项目起步依赖，通过该依赖自动关联其它依赖，不需要我们一个一个去添加了-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--SpringBoot框架的测试起步依赖，例如：junit测试，如果不需要的话可以删除-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!--SpringBoot提供的打包编译等插件-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```



项目目录结构：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210121143642447.png" alt="image-20210121143642447" style="zoom:50%;" />



- mvn、mvnw、mvnw.cmd：使用脚本执行maven相关命令，用的较少，可以删除
- gitignore：使用版本控制工具git时，设置忽略提交的内容
- static、templates：后面模板技术存放文件的目录
- application.properties：SpringBoot 的配置文件，集成的配置都可以在该文件中进行配置，例如Spring、SpringMVC、MyBatis和Redis等。
- Application.java：SpringBoot程序执行入口，执行该程序中main方法，SpringBoot就启动了。

SpringBoot项目代码要放在Application类的同级目录或下级目录中。

**即contoller、entity、dao、service、exception等包要和Application类放在同一级或下级目录中，并通过注解来设置。**

一般在Application类同级目录创建一个web包，把所有代码放在web包中。



在web包下创建一个控制器，仍然使用@Controller注解：

```java
@RestController
public class SpringBootController {


    @RequestMapping(value = "/springboot/sayHello")
    @ResponseBody
    public String sayHello() {
        return "Hello, Spring Boot";
    }
}
```



在DemoApplication运行main方法，启动该项目。

![image-20210121145751590](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210121145751590.png)



## 需要注意的几个地方

1. SpringBoot的父级依赖spring-boot-starter-parent配置之后，当前项目是一个SpringBoot项目。

2. spring-boot-starter-parent是一个SpringBoot 的父级依赖，开发SpringBoot程序都需要继承该父级项目，它用来提供相关Maven的默认依赖，使用它之后，常用的jar依赖可以省去version配置。

3. SpringBoot提供了哪些默认jar包的依赖，可以查看该父级依赖的pom文件

4. 如果不想使用默认的依赖版本，可以通过pom文件属性配置覆盖各个依赖项，比如覆盖spring版本：

    ```xml
    <properties>
    	<spring.version>5.0.0.RELEASE</spring.version>
    </properties>
    ```

5. @SpringBootApplication注解是SpringBoot的核心注解，主要的作用是开启Spring自动配置

6. main方法是标准的Java程序的main方法，作为项目启动运行的入口

7. @Controller及@ResponseBody仍然是之前学过的SpringMVC，因为SpringBoot里面仍然使用的是SpringMVC、Spring和MyBatis等框架。

