# 4-SSM整合开发

开发思路：
        SpringMVC：管理Controller层对象

Spring：管理Service和dao层，和工具类对象

MyBatis：负责数据访问



整个流程是：用户发起请求--->SpringMVC接受--->Spring中Service处理--->调用MyBatis处理数据

一共有两个容器：

- SpringMVC容器，管理COntroller控制器对象
- Spring容器，管理Service、Dao、工具类对象

要把合适的对象交给合适的容器管理。

Controller对象定义在springmvc配置文件中，Service、Dao对象定义在Spring配置文件中。

SpringMVC容器是Spring容器的子容器，类似Java的继承。子容器可以访问父容器的内容。

在子容器中的Controller可以访问父容器中Service对象。可以实现Controller调用Service对象。



## 开发步骤

SSM整个开发，有两种方式：基于XML配置文件方式，基于注解方式。

可以两种结合使用，以注解为主，XML配置为辅。

- 创建数据库表

- 创建Maven web项目

- 加入依赖：

    1. servlet
    2. jsp
    3. spring-tx：为JDBC、Hibernate等提供声明式和编程式事务管理
    4. spring-jdbc：对JDBC的简单封装
    5. spring-aspects：对AspectJ框架的整合
    6. spring-webmvc，该依赖包括：
        - spring-aop：Spring的面向切面编程，提供AOP的实现
        - spring-beans：Spring IOC的基础实现，包括访问配置文件，创建和管理bean等
        - spring-content：在基础IOC服务上，提供拓展服务。
        - spring-core：Spring的核心工具包
        - spring-expressions：Spring表达式语言
        - spring-web：包含web应用开发时，用到Spring框架所需的核心类
    7. mybatis依赖
    8. mybatis和spring整合后依赖：mybatis-spring
    9. mysql驱动依赖
    10. druid，阿里数据池依赖
    11. jackson：json数据格式转换依赖，包括jackson-core和jackson-databind
    12. junit：单元测试
    13. annotion注解依赖依赖，javax.annotion-api。该包属于Java拓展库，需要手动导入
    14. lombok，不多介绍

- pom文件设置包括资源文件编译

    ```xml
    <resources>
          <resource>
            <directory>src/main/java</directory>
            <includes>
              <include>**/*.xml</include>
              <include>**/*.properties</include>
            </includes>
            <filtering>false</filtering>
          </resource>
        </resources>
    ```

    

- 配置web.xml

    1. 注册ContextLoaderListener监听器：创建Spring容器，并将容器对象放入ServletContext作用域

        ```xml
            <context-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:springmvc配置文件路径</param-value>
            </context-param>
            <listener>
                <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
            </listener>
        ```

        

    2. 注册字符集过滤器：解决请求参数中中文乱码问题

        ```xml
        <filter>
                <filter-name>characterEncodingFilter</filter-name>
                <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
                <init-param>
                    <param-name>encoding</param-name>
                    <param-value>utf-8</param-value>
                </init-param>
                <init-param>
                    <param-name>forceRequestEncoding</param-name>
                    <param-value>true</param-value>
                </init-param>
                <init-param>
                    <param-name>forceResponseEncoding</param-name>
                    <param-value>true</param-value>
                </init-param>
            </filter>
            <filter-mapping>
                <filter-name>characterEncodingFilter</filter-name>
                <url-pattern>/*</url-pattern>
            </filter-mapping>
        ```

        

    3. 配置中央调度器DispatherServlet，创建SpringMVC容器

        ```xml
            <servlet>
                <servlet-name>springmvc</servlet-name>
                <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
                <init-param>
                    <param-name>contextConfigLocation</param-name>
                    <param-value>classpath:spring配置文件路径</param-value>
                </init-param>
                <load-on-startup>1</load-on-startup>
            </servlet>
        
            <servlet-mapping>
                <servlet-name>springmvc</servlet-name>
                <url-pattern></url-pattern>
            </servlet-mapping>
        ```

        

- 创建实体类

- 创建DAO接口

- 创建Mapper映射文件

- 设置MyBatis配置文件

- 创建Service接口和实现类

- 创建属性文件，jdbc.properties

- spring配置文件

    1. 声明组件扫描器
    2. 配置数据源
    3. 注册SqlSessionFactoryBean
    4. 注册动态代理对象
    5. 注册Service对象

- springmvc配置文件

    1. 声明组件扫描器：`<context:component-scan base-package=""/>`

    2. 注册注解驱动：`<mvc:annotation-driven/>`

    3. 指定视图解析器

        ```xml
        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
                <property name="prefix" value=""/>
                <property name="suffix" value=""/>
            </bean>
        ```

    4. 使用<mvc:resources mapper=""  location="" />标签，声明哪些静态资源能够访问

- 创建Controller类

- 创建视图页面，JS和css等，发送请求，处理返回数据。

    

    

    

    

    

## 一个例子

完成对学生表的查找和插入。

学生表：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201219091945650.png" alt="image-20201219091945650" style="zoom:50%;" />

### 加入pom依赖和编译包括xml和properties

```xml
<dependencies>
    <!--单元测试-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <!--Servlet,一定要用3.x版本-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.0.1</version>
    </dependency>
    <!--JSP-->
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>javax.servlet.jsp-api</artifactId>
      <version>2.3.3</version>
    </dependency>
    <!--WEB依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.3.1</version>
    </dependency>
    <!--数据库事务管理-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>5.3.1</version>
    </dependency>
    <!--AspectJ框架-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>5.3.1</version>
    </dependency>
    <!--数据库访问-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.3.1</version>
    </dependency>
    <!--MySql驱动-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.21</version>
    </dependency>
    <!--阿里数据连接池-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.2.3</version>
    </dependency>
    <!--MyBatis框架-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.6</version>
    </dependency>
    <!--MyBatis和Spring整合-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>2.0.6</version>
    </dependency>
    <!--Json数据格式转换-->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.12.0</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.12.0</version>
    </dependency>
    <!--Java注解-->
    <dependency>
      <groupId>javax.annotation</groupId>
      <artifactId>javax.annotation-api</artifactId>
      <version>1.3.2</version>
    </dependency>
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.16</version>
    </dependency>
  </dependencies>

  <build>
    <resources>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.xml</include>
          <include>**/*.properties</include>
        </includes>
        <filtering>false</filtering>
      </resource>
    </resources>
  </build>
```



### 配置web.xml

#### 注册DispatherServlet

```xml
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:conf/springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>*.do</url-pattern>
    </servlet-mapping>
```

#### 注册ContextLoaderListener监听器

```xml
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:conf/applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
```

#### 注册字符集拦截器

```xml
<!--设置编码格式-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
            <param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```



### 创建实体类

实体类Student(用到了Lombok)

```java
@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@ToString
public class Student {
    @NonNull
    private Integer id;
    @NonNull
    private String name;

    private String email;
    @NonNull
    private Integer age;
}
```

### 创建DAO接口

StudentDao接口

```java
public interface StudentDao {
    int insertStudent(Student student);
    List<Student> selectStudents();
}
```

### 创建MyBatis映射文件

```xml
<mapper namespace="org.example.dao.StudentDao">
    <insert id="insertStudent">
        insert into student(name, email, age) value (#{name}, #{email}, #{age})
    </insert>
    <select id="selectStudents" resultType="org.example.entity.Student">
        select id, name, email, age from student
    </select>

</mapper>
```



### 创建MyBatis配置文件

```xml
<configuration>
<!--关掉了日志-->
<!--    &lt;!&ndash;设置&ndash;&gt;-->
<!--    <settings>-->
<!--        &lt;!&ndash;输出日志到控制台&ndash;&gt;-->
<!--        <setting name="logImpl" value="STDOUT_LOGGING"/>-->
<!--    </settings>-->

    <!--设置别名-->
    <typeAliases>
        <package name="org.example.entity"/>
    </typeAliases>

    <!--映射文件配置,接口和映射文件名称完全一样，并且在同一目录，才能使用package标签-->
    <mappers>
        <package name="org.example.dao"/>
    </mappers>
	
</configuration>
```

### 创建Service接口和实现类

StudentService接口

```java
public interface StudentService {

    int addStuent(Student student);
    List<Student> selectStudents();
}
```

StudentServiceImpl实现类

```java
@Service
public class StudentServiceImpl implements StudentService {
	//使用Resource注解自动注入，先ByName，再ByType
    @Resource
    private StudentDao studentDao;
    @Override
    public int addStuent(Student student) {
        int num = studentDao.insertStudent(student);
        return num;
    }

    @Override
    public List<Student> selectStudents() {
        return studentDao.selectStudents();
    }
}
```

### 创建属性文件jdbc.properties

```properties
jdbc.url = jdbc:mysql://localhost:3306/test
jdbc.username = root
jdbc.password = *********
jdbc.maxActive = 20
```

### spring配置文件

#### 声明组件扫描器

用来扫描Service类对象

```xml
    <context:component-scan base-package="org.example.service"/>
```

#### 配置数据源

```xml
    <!--配置属性文件路径-->
    <context:property-placeholder location="classpath:conf/jdbc.properties"/>


    <!--配置数据源，使用${}来引用属性文件-->
    <bean id="druid" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <property name="maxActive" value="${jdbc.maxActive}"/>
    </bean>
```

#### 注册SqlSessionFactoryBean

```xml
    <!--创建SqlSessionFactory对象-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="druid"/>
        <property name="configLocation" value="classpath:conf/mybatis.xml"/>
    </bean>
```

#### 注册动态代理对象

注册MapperScannerConfigurer对象，批量生成接口的代理对象

```xml
<!--创建dao接口代理对象-->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
    <property name="basePackage" value="org.example.dao"/>
</bean>
```

这次没有配置事务管理。



### springmvc配置文件

#### 声明组件扫描器

用来扫描controller对象

```xml
<!--组件扫描器-->
<context:component-scan base-package="org.example.controller"/>
```

#### 注册注解驱动

```xml
<!--注解驱动-->
<mvc:annotation-driven/>
```

#### 指定视图解析器

```xml
<!--注册视图解析器，前缀和后缀-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/views/"/>
    <property name="suffix" value=".jsp"/>
    <property name="order" value="0" />
</bean>
```

#### 声明静态资源访问标签

```xml
    <!--处理静态资源访问,location为静态资源目录,格式"/dir/"，mappring为静态资源目录所有文件映射,格式"/dir/**"-->
    <mvc:resources mapping="/static/**" location="/static/"/>
    <mvc:resources mapping="/views/**" location="/views/"/>
```



**注意，视图解析器用来处理器方法返回视图时，用来解析视图路径的。而静态资源访问标签，是用来让前端直接访问静态资源的，**



### 创建Controller类

```java
@Controller
@RequestMapping(value ="/student")
public class StudentController {

    @Resource
    private StudentService service;

    @RequestMapping(value = "/addStudent.do")
    public ModelAndView addStudent(Student student) {
        ModelAndView mv = new ModelAndView();
        String tip;
        int num = service.addStuent(student);
        System.out.println(num);
        if(num > 0) {
            tip = "学生[" + student.getName() + "]注册成功";
        } else {
            tip = "学生[" + student.getName() + "]注册失败";
        }
        mv.addObject("tip", tip);
        mv.setViewName("result");
        return mv;
    }

    @RequestMapping(value = "/listStudents.do")
    @ResponseBody
    public List<Student> listStudents() {
        System.out.println("收到请求");
        List<Student> list = service.selectStudents();
        return list;
    }
}
```



### 创建页面，jsp、js等文件

#### 索引页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
    String basePath = request.getScheme() + "://" + request.getServerName()
                        + ":" + request.getServerPort() + request.getContextPath() + "/";
%>
<html>
<head>
    <base href="<%=basePath%>views/">
</head>
<body>
<h2>SSM整合开发</h2>
<a href="register.jsp">学生注册</a><br>
<a href="listStudents.jsp">查看学生信息</a>
</body>
</html>
```

#### 注册页面

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
                    <td>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</td>
                    <td><input type="submit" value="注册"></td>
                </tr>
            </table>
        </form>
    </div>
</body>
</html>
```

#### 注册成功页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <div align="center">
        <img src="../static/images/1.png" width="800px" height="600px">
        ${tip}
    </div>
    
</body>
</html>
```

#### 查看所有学生页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>查看学生</title>
<%--    <script type="text/javascript" src="../static/js/jquery_3.5.1.js"></script>--%>
    <script type="text/javascript">
        window.onload = function (){

            let xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if(xmlHttp.readyState == 4 && xmlHttp.status == 200) {
                    let json = xmlHttp.responseText;
                    let data = JSON.parse(json);
                    document.getElementById("stuBody").innerHTML = "";
                    for(let i = 0; i < data.length; i++){
                        document.getElementById("stuBody").innerHTML =
                            document.getElementById("stuBody").innerHTML +  "<tr>"
                            + "<td>" + data[i].id + "</td>"
                            + "<td>" + data[i].name + "</td>"
                            + "<td>" + data[i].age + "</td>"
                            + "<td>" + data[i].email + "</td>"
                            + "</tr>";
                    }
                }
            }

            xmlHttp.open("post", "/myweb/student/listStudents.do");
            xmlHttp.send();

        }
    </script>
</head>
<body>
    <div align="center">
        <p>查询学生数据</p>
        <table border="1px" frame="box" rules="all" style="font-size: 25px" align="center">
            <thead>
                <tr>
                    <td>ID</td>
                    <td>姓名</td>
                    <td>年龄</td>
                    <td>Email</td>
                </tr>
            </thead>
            <tbody id="stuBody">

            </tbody>
        </table>

    </div>
</body>
</html>
```