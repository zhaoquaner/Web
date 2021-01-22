# 4-SpringBoot集成MyBatis和事务

## 集成MyBatis



集成MyBatis很简单：

1. 添加依赖
2. 配置文件中配置数据源
3. 使用MyBatis逆向工程生成映射文件和实体类，DAO接口，并在接口类中添加注解@Mapper



### 添加依赖

SpringBoot集成MyBatis，需要添加两个依赖：

1. SpringBoot中MyBatis的起步依赖，这个依赖在spring-boot-dependencies中没有，需要手动指定版本号。这是MyBatis提供的依赖
2. MYSQL驱动依赖，不需要指定版本号，spring-boot-dependencies中已经设置好了。

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.4</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```



### 配置数据源

在application.properties配置文件中，配置数据源：

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=
```

在默认情况下，MyBatis的xml映射文件不会编译到target的class目录中，

所以在pom文件中的build标签中，添加编译xml文件，配置resource标签。

```xml
<resource>
    <directory>src/main/java</directory>
    <includes>
        <include>**/*.xml</include>
    </includes>
</resource>
```

​	

### 逆向工程生成DAO接口，映射文件和实体类

逆向工程需要配置GeneratorConfig.xml文件，具体详见MyBatis笔记。

在SSM项目中，有一个DAO组件扫描器注册在Spring配置文件中。

在SpringBoot项目中，使用@Mapper注解即可，表示将该对象加入到Spring容器中。





做完这三步，剩下就和SSM没什么区别了，正常使用。





### DAO另一种开发方式

可以去掉DAO接口类的@Mapper接口

然后 运行的主类Application类上添加注解包扫描@MapperScan(basePackages="com.example.demo.mapper")

也可以去掉basePackages，直接写对应包路径。



### 将接口和映射文件分开

因为SpringBoot不能自动编译接口映射的xml文件，需要在pom.xml文件中手动指定。所以也可以将映射文件直接放在resources目录下，即在resources目录下新建目录mapper存放映射文件。

并在application.properties配置文件中，指定映射文件的位置，格式为：

```properties
mybatis.mapper-locations=classpath:mapper/*.xml
```

这个配置只有在接口和映射文件不在同一个包的情况下，才需要指定。



### 使用Druid数据池

使用阿里的Druid，那么就首先的pom文件中引入druid的springboot起步依赖，druid-spring-boot-starter，然后在配置文件中加入：

```properties
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.druid.url=jdbc:mysql://localhost:3306/test
spring.datasource.druid.username=root
spring.datasource.druid.password=
spring.datasource.druid.max-active=20
```

就可以正常使用了。





## 集成事务

使用事务也非常简单。只需要在服务接口的继承类需要事务的方法上加上注解@Transactional。

就可以开启事务。

例如：

```java
@Service
public class StudentServiceImpl implements StudentService {

    @Resource
    private StudentMapper studentMapper;
    	
    @Transactional
    @Override
    public Student queryStudentById(Integer id) {
        return studentMapper.selectByPrimaryKey(id);
    }
}
```





在Spring之前版本中还需要在启动类Application类上加上@EnableTransactionManagement，来开启事务。

但是在新版本中已经不需要了。所以这个注解加不加都行。