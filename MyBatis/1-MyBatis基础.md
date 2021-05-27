# 1-MyBatis基础



## 基本概念



​		MyBatis框架是一个基于Java的持久层(数据访问层)框架，内部封装了JDBC,开发者只需要关注SQL语句本身，将处理加载驱动、创建连接、创建statement、关闭连接、资源等繁琐的过程全都交给MyBatis来处理。

​		MyBatis通过XML或注解两种方式将要执行的各种sql语句配置起来，并通过Java对象和sql的动态参数进行映射生成最终的sql语句，最后由MyBatis框架执行sql并将结果映射为java对象返回。



​		很重要的一个概念是映射，它的意思是数据库中每一行的数据都可以映射为一个Java对象，我们会创建对应的实体类，保持成员变量和表中属性一致。





创建数据库表`Student(id int, name varchar(255), email varchar(255), age int)`

其中主键是id。

创建实体类Student，放在包org.example.domain中。

```java
public class Student {

    private Integer id;
    private String name;
    private String email;
    private Integer age;
    
    //set和get方法
}
```

创建StudentDao接口，放在包org.example.dao中。

```java
public interface StudentDao {

    public List<Student> selectStudents();
}
```



在之后，都是以这个作为例子。

		## 使用MyBatis步骤



### 创建Mysql表

首先要在Mysql中创建相应表。

### 创建对应实体类

在Java中创建实体类，使成员变量和表中属性一致

### 创建持久层的DAO接口

在持久层创建该表的DAO接口，定义操作该表的方法

### 创建sql映射文件

这个是MyBatis使用的配置文件，是用来写sql语句的。一般，一个表一个sql映射文件。

该文件应和DAO接口放在同一文件中，文件名也和接口保持一致。

### 创建主配置文件

这个配置文件时MyBatis的主配置文件，一个项目中只有一个主配置文件，它提供了数据库的连接信息和sql映射文件的位置信息。

### 使用mybatis访问数据库(使用SqlSession，最简单的一种情况)

#### 设置主配置文件路径

使用一个字符串变量表示主配置文件路径

#### 获得主配置文件的输入流

使用MyBAtis框架中的`Resources.getResourceAsStream(config)`来获取配置文件输入流，定义InputStream变量来获得返回值。

#### 创建SqlSessionFactoryBuilder对象和SqlSessionFactory对象

创建一个SqlSessionFactoryBuilder对象，它有一个build方法，将上述的输入流参数传入该方法，得到SqlSessionFactory对象，这个对象是用来生成数据库访问会话的。

#### 设置sql语句的id

在sql映射文件中，有sql语句id，将namespace和id拼接形成的字符串作为sql语句的id

#### 使用session，会话执行SQL语句，返回结果

调用factory的openSession()方法，打开一个会话session，调用会话的selectList方法，将上面的id传入该方法，作为参数。该方法执行查询并返回一个List集合，集合里元素类型就是sql映射文件中resultType类型。





**注意：上述的访问数据库的方法，只是其中一种，还有很多其他的重载方法可用。**







## SQL映射文件

​		该文件是用来写sql语句的，MyBatis框架会执行这些sql

​	

基本文件格式为：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.example.dao.StudentDao">
    <select id="" resultType="">
    select * from Blog where id = #{id}
  </select>
</mapper>
```

其中

1. 第一行是用来规定xml的版本和字符集。

2. mybatis-3-mapper.dtd是约束文件名称，拓展名为dtd。

    约束文件的作用：

    ​		是用来限制、检查当前文件中出现的标签、属性是否符合MyBatis的要求。

    很多框架的配置文件中都会用到约束文件。

3. mapper

    是当前文件的根标签，是必须有的。

    namespace：叫做命名空间，唯一值，可以是自定义的字符串，但是应该使用dao接口的全限定名称，例如StudentDao接口，那么namespace就应该等于"org.example.dao.StudentDao"

在当前文件中，可以使用特定的标签来表示数据库的特定操作。

- `<select>`：表示查询操作
- `<update>`：表示更新操作
- `<insert>`：插入操作
- `<delete>`：删除操作



### 操作

#### select

`<select>`表示查询操作。

属性：

- id：要执行的sql语句的唯一标识，MyBatis会使用这个id的值来找到要执行的sql语句。相等于给这个操作语句起了个名。

    ​		可以自定义，但是要求使用接口中的方法名称。

- resultType：表示结果类型，即数据库的每一行应该映射为什么Java类型。

    ​						值是该类型的全限定名称。

例子：

在DAO接口中定义查询方法：

```java
    public List<Student> selectStudents();
```

在映射文件中编写具体查询的sql语句：

```xml
    <select id="selectStudents" resultType="org.example.domain.Student" >
        select id, name, email, age from student;
    </select>
```

使用SqlSession的`selectList(String statement)`方法来进行查询，返回值是List集合。selectList有多种重载方法。

#### insert

插入操作

语法格式

```xml
<insert id="">
    insert into tablename values(#{}, #{}, #{}...)
</insert>    
```

其中属性id是这个插入操作的唯一标识，应使用DAO接口的对应方法名。

语法格式：`#{}`代表访问传来的参数对象中的成员变量。

例如：

在DAO接口中定义插入数据方法:

```java
    public int insertStudent(Student student);
```

参数是Student对象，在映射文件中使用#{}来访问该对象中的成员变量作为实际插入的数据：

```xml
    <insert id="insertStudent" >
        insert into student values (#{id}, #{name}, #{email}, #{age})
    </insert>
```

使用SqlSession的`insert(String Statement, Object parameter)`来执行插入语句，第一个参数表示sqlId，第二个表示要传入的参数。insert有两种重载方法。另一种是`insert(String statement)`。



#### update

更新操作。

属性id 和上述一样。

其中同样使用`#{}`来访问参数对象的成员变量。

例子：

在DAO接口定义更新对象方法：

```java
    public int updateStudent(Student student);
```

参数Student对象，在映射文件中定义具体sql语句：

```xml
    <update id="updateStudent" >
        update student set age = #{age} where name = #{name}
    </update>
```

使用SqlSession的`update(String statement, Object parameter)`对象执行更新操作。该方法有两种重载形式。另一种是`update(String statement)`。



执行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201202101656428.png" alt="image-20201202101656428" style="zoom:40%;" />





#### delete

删除操作

属性id 和上面一样。

同样使用`#{}`访问参数对象成员变量。

例子：
DAO接口增加删除方法：

```java
    public int deleteStudent(Student student);
```

映射文件中编写具体删除的sql语句：

```xml
    <delete id="deleteStudent" >
        delete from student where name = #{name}
    </delete>
```

使用SqlSession的delete方法来执行删除操作。同样有两种重载类型：`delete(String statement, Object parameter)`和`delete(String statement)`。



执行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201202102209579.png" alt="image-20201202102209579" style="zoom:43%;" />









## 主配置文件

基本格式为

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```



主配置文件放在resources目录中，是xml文件，在头部使用约束文件。它的根元素是<configuration>,主要包含定义别名、配置数据源、mapper文件三个内容。

其中：

- `<environments>`：表示环境配置，即数据库的连接信息。

    default属性的值必须和某个environment的id值一样，这个属性值告诉mybatis使用哪个数据库的连接信息

- `<environment>`：表示一个数据库的配置环境。属性id表示该数据库配置的名称，自定义，是一个唯一值

- `<transactionManager>`：表示mybatis使用的事务管理器

    type代表类型，有两个可取值:

    1. JDBC(表示使用JDBC中的Connection对象的commit、rollback)。默认情况下，MyBatis使用手动提交，即需要在程序中显式的对事务进行提交或回滚。
    2. MANAGED：表示由容器来管理事务的整个生命周期(如Spring容器)。

- `<dataSource>`：表示数据源，type代表数据源类型，有三种取值

    1. POOLED：使用连接池，MyBatis会创建PooledDataSource实例
    2. UNPOOLED：MyBatis会创建UnPooledDataSource实例
    3. JNDI：PooledDataSource会从JNDI服务上查找DataSource实例，然后返回使用。

- `<property>`：表示连接的属性配置，有驱动，url，用户名，密码四个要素。

    属性name有driver、url、username、password取值，属性value表示实际的值



此外，为了方便对数据库连接的管理，数据库连接的四要素一般放在专门的属性文件中，拓展名为properties。MyBatis需要从属性文件中读取这些数据。

同样把属性文件放在resources目录中，名称自定义。

例如: jdbc.properties，文件基本格式为

```
jdbc.driver=
jdbc.url=
jdbc.username=
jdbc.password=
```



然后在主配置文件<configuration>标签下，加入<properties resource = "文件路径" />标签，加载该属性文件。

**然后在 <property>标签中的value属性，使用${key}语法格式来获得属性文件中的对应值。**

例如:$(jdbc.driver)即可获取驱动。



<mappers>标签指定sql应设问句的位置，其中一个<mapper>标签指定一个文件的位置，属性resource就用来指定文件路径。

类路径，即编译后的target/classes目录下开始的路径。



也可以使用<package name="">用来指定包下的所有DAO接口

使**用package的要求：**

- **mapper文件和dao接口文件名必须完全一样，包括大小写**
- **mapper文件和dao接口必须在同一目录**



## 一个例子

根据之前创建的student表和Student实体类，来使用MyBatis框架访问数据库。

首先创建sql映射文件StudentDao.xml：

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.example.dao.StudentDao">
    <select id="selectStudents" resultType="org.example.domain.Student" >
        select id, name, email, age from student;
    </select>
</mapper>
```

在resources目录中创建jdbc.properties属性文件和mybatis.xml主配置文件：

```xml
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/test?usrSSL=false&serverTimezone=UTC
username=root
password=*******
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="jdbc.properties" />
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="org/example/dao/StudentDao.xml"/>
    </mappers>
</configuration>
```

在测试类中编写测试代码：

```java
    @Test
    public void test1() throws IOException {
        //设置主配置文件路径
        String config = "mybatis.xml";
        //获取输入流
        InputStream in = Resources.getResourceAsStream(config);
        //创建sqlSessionFactoryBuilder对象，用该对象构建SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        //将输入流变量in传入，构建SqlSessionFactory
        SqlSessionFactory factory = builder.build(in);
        //调用factory方法打开一个会话
        SqlSession session = factory.openSession();
        //拼接namespace和id作为sql语句的id
        String sqlId = "org.example.dao.StudentDao" + "." + "selectStudents";
		//将id传入执行方法获得结果List集合
        List<Student> list = session.selectList(sqlId);
        list.forEach(sql-> System.out.println(sql));
    }
```

运行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201201141739513.png" alt="image-20201201141739513" style="zoom:43%;" />









## 注意

1. 

默认情况下，Maven编译是不会加上xml、properties等文件的，如果要让Maven加上这些文件，要在pom.xml中的build标签中，加入一个插件：

```xml
<build>
	<resources>
      <resource>
          <!--扫描的目录-->
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

2. 在mybatis.xml文件中加入如下代码，可以将数据库操作日志输出到控制台，方便来查看具体信息。

    ```xml
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    ```





