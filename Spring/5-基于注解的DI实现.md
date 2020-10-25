# 5-基于注解的DI实现

通过注解来配置信息是为了简化IOC容器的配置，注解可以把对象添加到IOC容器中、处理对象依赖关系。

## 使用注解步骤

1. 加入maven的依赖spring-context，加入的同时，也加入了spring-aop的依赖，使用注解必须要使用spring-aop依赖
2. 在类中加入spring的依赖
3. 在spring配置文件中，加入一个组件扫描器的标签，来说明注解在项目中的位置



## 加入组件扫描器

在配置文件中加入组件扫描器的标签 `<context:component-scan base-package="" />`

属性：base-package，指定注解要扫描的包名

扫描器的工作方式：spring会扫描遍历base-package指定的包，递归的找到所有类中的注解，按照注解的功能创建对象或者给属性赋值。

context是指对应的命名空间，`xmlns:context="http://www.springframework.org/schema/context"`。

### 指定多个包的方式

有三种方式

1. 使用多个`<context:component-scan base-package="" />`语句，指定不同的包
2. 使用分隔符(; 或 ,)分割多个包名，例如`<context:component-scan base-package="org.example.package1;org.example.package2"`
3. 指定需要扫描的所有包的父包，例如package1和package2的父包是org.example，那么就直接指定这个包即可



## 主要学习的注解

有以下几个：

- @Component
- @Respotory
- @Service
- @Controller
- @Value
- @Autowired
- @Resource



### @Component

这个注解用来创建对象，等同于标签bean，

属性：value，表示这个对象的名称，是唯一的。也可以省略value，直接写对象名称，必须加引号

​				如果不写这个属性，那么spring会使用默认的对象名称：类名的首字母小写，例如类名为Student，那么默认名称为                      				student

位置：在类的上面

例子：

```java
package org.example.point1;

import org.springframework.stereotype.Component;

@Component(value = "myStudent") //指定对象的唯一名称
public class Student {

    private String name;
    private Integer age;

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```





### 与@Component用法类似的三个注解

1. @Repository(用在持久层类上面)：放在DAO的实现类上面，表示创建DAO对象，DAO对象可以访问数据库
2. @Service(用在业务层类上面)：放在service的实现类上面，创建service对象，该对象是做业务处理，有事务等功能
3. @Controller(用在控制器上面)：放在控制器类上面，创建控制器对象，能够接受用户提交的参数，显示请求处理结果，如Servet

这三个注解和@Comonent的用法大致相同。都可以创建对象，但是这三个注解还有额外的功能。

@Repository，@Service，@Controller是用来给项目对象分层的，它们可以赋予对象不同的角色。是比@Component的功能丰富的。



当一个类不是以上三个类型，或者不知道是不是这三个类，就可以使用@Component



### @Value(简单类型的赋值)

​	用来给简单类型的属性赋值

​	属性：value，String类型，表示简单类型的属性值。可不写value，直接写属性值。必须加引号

​	位置： 

1. 在属性定义上面，不需要set方法，推荐使用这种
2. 在set方法上面

例子：

```java
@Component
public class Student {

    @Value("Jack")
    private String name;
    @Value("21")
    private Integer age;

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

当然也可以使用在set方法上，用的很少，不再举例了。



### 引用类型的赋值

两个注解@Autowired和@Resource都可以给引用类型赋值。

#### @Autowired

实现引用类型的赋值，使用的是自动注入原理。支持byName、byType

@Autowired默认使用的是byType自动注入。

属性：required，布尔类型，默认为true

​			当required=ture：表示如果引用类型赋值失败，程序报错，并终止执行

​			当required=false：表示如果引用类型赋值失败，程序正常运行，引用类型为null

**推荐使用true。**

位置：

1. 在属性定义上面，无需set方法，推荐使用这个
2. 在set方法上面，很少使用

例子：

定义引用类型School：

```java
@Component("mySchool")
public class School {

    @Value("清华大学")
    private String name;
    @Value("北京海淀区")
    private String address;

    @Override
    public String toString() {
        return "School{" +
                "name='" + name + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}

```

Student的代码：

```java
@Component
public class Student {

    @Value("Jack")
    private String name;
    @Value("21")
    private Integer age;
    @Autowired //使用Autowired来进行引用类型注入
    private School school;

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", school=" + school +
                '}';
    }
}

```



##### 使用byName方式

如果要使用byName方式，那么还需要在属性上面加入@Qualifier(value="bean的id")：表示使用指定名称的bean完成赋值

位置

在上面代码@Autowired的上面或者下面加入`@Qualifier(value="mySchool")`，就可以实现byName方式赋值



#### @Resource

来自jdk的注解，spring框架提供了对这个注解的功能支持，可以使用它给引用类型赋值

使用的也是自动注入原理，支持byName、byType，默认使用byName

属性：name：当使用byName方法时，指定bean的id，**name不能省略**；使用byType方法，不需要这个属性

位置：

1. 在属性定义上面，无需set方法，推荐
2. 在set方法上面

**注意：**

- **如果没有指定name属性，会使用byType；如果指定了name属性，那么使用byName，如果找不到，会报错**

- **这个注解在javax包，也就是java的拓展包里，在java 11以后，jdk不再包括javax，所以需要在pom.xml中手动导入。**



例子：

```java
@Component
public class Student {

    @Value("Jack")
    private String name;
    @Value("21")
    private Integer age;
    @Resource
    private School school;

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", school=" + school +
                '}';
    }
}

```

可以看到，@Resource没有使用name属性，但是程序可以运行成功，就是因为byName失败后，自动使用byType赋值。

​	



## 配置文件和注解两种方式

这两种方式都可以使用，但是更多的是使用注解。注解为主，配置文件为辅。

但是如果修改较多的话，那么就可以使用配置文件。

如果不需要怎么改变修改的，就可以使用注解。

