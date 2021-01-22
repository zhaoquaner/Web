# 3-SpringBoot使用JSP

SpringBoot推荐使用Thymeleaf模板技术(后面会讲)。所以如果要使用JSP就要手动配置。



在之前的学习中，jsp文件都是放在webapp目录中的，而在刚创建好的SpringBoot项目中没有webapp目录，

所以要手动创建webapp目录并在Project Structure中将webapp目录设置为Web Resource 目录。



并添加相应依赖：

```xml
<!--引入Spring Boot内嵌的Tomcat对JSP的解析包，不加解析不了jsp页面-->
<!--如果只是使用JSP页面，可以只添加该依赖-->
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
</dependency>

<!--如果要使用servlet必须添加该以下两个依赖-->
<!-- servlet依赖的jar包-->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
</dependency>

<!-- jsp依赖jar包-->
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>javax.servlet.jsp-api</artifactId>
    <version>2.3.1</version>
</dependency>

<!--如果使用JSTL必须添加该依赖-->
<!--jstl标签依赖的jar包start-->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
</dependency>
```



然后要手动指定jsp最后编译的路径。SpringBoot要求jsp文件必须要编译到META-INF/resources目录中才能访问到，否则访问不到。

在pom.xml中配置：

```xml
<bulid>
    <resources>
        <resource>
            <!--源文件位置-->
            <directory>src/main/webapp</directory>
            <!--指定编译到META-INF/resource，该目录不能随便写-->
            <targetPath>META-INF/resources</targetPath>
            <!--指定要把哪些文件编译进去，**表示webapp目录及子目录，*.*表示所有文件-->
            <includes>
                <include>**/*.*</include>
            </includes>
        </resource>
    </resources>
</bulid>
```



然后在application.properties中配置SpringMVC的视图解析器。其实就相当于SpringMVC的配置：

```properties
# 其中:/相当于src/main/webapp
spring.mvc.view.prefix=/
spring.mvc.view.suffix=.jsp
```

所有这些配置完成以后，使用JSP就和之前一模一样了。



## 总结

使用JSP步骤为：

1. 创建webapp目录，并在目录结构中指定该目录为Web Resources Directory
2. 加入springboot的jsp依赖，以及servlet和servlet-jsp依赖，如要使用jstl，则加入jstl依赖
3. 在apolication.properties中配置SpringMVC的视图解析器。

