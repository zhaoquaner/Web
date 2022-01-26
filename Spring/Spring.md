# 1-Spring简述

Spring全家桶有很多东西，包括Spring Boot、Spring Cloud、Spring MVC等等。Spring Framework是最基本的Spring 框架。

Spring帮助开发人员创建对象、管理对象之间的关系。spring的核心技术IOC、AOP，能实现模块之间，类之间的解耦合。

简单来说，IOC解决的是 对象管理和对象依赖的问题。

AOP解决的是 非业务代码抽取的问题。

Spring分为六大模块：

- Spring Core：核心功能，IOC容器，解决对象创建和依赖关系
- Spring Web：Spring对web模块的支持
    - 可以和struts整合，让struts的action创建交给spring
    - spring mvc模式
- Spring DAO：Spring对JDBC操作对的支持
- Spring ORM：spring对ORM的支持
    - 既可以和Hibernate整合
    - 也可以使用spring的Hibernate操作的封装
- Spring AOP：切面编程
- SpringEE：spring对JavaEE其他模块的支持





# 2-IOC

## 概念

控制反转(IOC)，是一种概念，一种思想。指将传统上由程序代码直接操纵的对象的调用权交给容器，通过容器来实现对象的装配和管理。控制反转指的就是 对对象控制权的转移，从程序代码本身反转到了外部容器。通过容器实现对象的创建、属性赋值、依赖的管理。

 	IOC是一种思想，它的实现方法有很多，比较流行的是依赖注入(Dependency Injection) ，也叫DI，应用广泛。

如何理解IOC(截取知乎上一篇文章的核心部分):

> ioc的思想最核⼼的地⽅在于，资源不由使⽤资源的双⽅管理，⽽由不使⽤资源的第三⽅管理，这
> 可以带来很多好处。第⼀，资源集中管理，实现资源的可配置和易管理。第⼆，降低了使⽤资源
> 双⽅的依赖程度，也就是我们说的耦合度。
> 也就是说，甲⽅要达成某种⽬的不需要直接依赖⼄⽅，它只需要达到的⽬的告诉第三⽅机构就可
> 以了，⽐如甲⽅需要⼀双袜⼦，⽽⼄⽅它卖⼀双袜⼦，它要把袜⼦卖出去，并不需要⾃⼰去直接
> 找到⼀个卖家来完成袜⼦的卖出。它也只需要找第三⽅，告诉别⼈我要卖⼀双袜⼦。这下好了，
> 甲⼄双⽅进⾏交易活动，都不需要⾃⼰直接去找卖家，相当于程序内部开放接⼝，卖家由第三⽅
> 作为参数传⼊。甲⼄互相不依赖，⽽且只有在进⾏交易活动的时候，甲才和⼄产⽣联系。反之亦
> 然。这样做什么好处么呢，甲⼄可以在对⽅不真实存在的情况下独⽴存在，⽽且保证不交易时候
> ⽆联系，想交易的时候可以很容易的产⽣联系。甲⼄交易活动不需要双⽅⻅⾯，避免了双⽅的互
> 不信任造成交易失败的问题。因为交易由第三⽅来负责联系，⽽且甲⼄都认为第三⽅可靠。那么
> 交易就能很可靠很灵活的产⽣和进⾏了。这就是ioc的核⼼思想。⽣活中这种例⼦⽐⽐皆是，⽀付
> 宝在整个淘宝体系⾥就是庞⼤的ioc容器，交易双⽅之外的第三⽅，提供可靠性可依赖可灵活变更
> 交易⽅的资源管理中⼼。另外⼈事代理也是，雇佣机构和个⼈之外的第三⽅。
> ==========================update==============
> 在以上的描述中，诞⽣了两个专业词汇，依赖注⼊和控制反转所谓的依赖注⼊，则是，甲⽅开放
> 接⼝，在它需要的时候，能够讲⼄⽅传递进来(注⼊)所谓的控制反转，甲⼄双⽅不相互依赖，交易
> 活动的进⾏不依赖于甲⼄任何⼀⽅，整个活动的进⾏由第三⽅负责管理。

### 什么是依赖

classA中有classB的实例，那么在classA中调用classB的方法完成实例，那么classA对classB有依赖。



依赖注入：是指，在程序运行过程中，若需要调用另一个对象协助时，无须再代码中创建，而是依赖于外部容器，由外部容器创建后传递给程序。

Spring的依赖注入对调用者和被调用者几乎没有任何要求，完全支持对象之间依赖关系的管理。



IOC的另一个体现是在使用Servlet时，没有创建过Servlet对象，而是由Tomcat来创建，所以也叫Tomcat容器。



## 创建对象

在spring中，把Java对象叫做bean。

在使用maven管理项目时，在main目录下，创建resources目录，在该目录下创建spring的配置文件beans.xml。

在这个配置文件中，配置spring需要创建的类对象。格式为：

```xml
<bean id="" class="" />
```

声明bean，就代表告诉spring创建某个类的对象，id是该对象的自定义名称，是唯一值，spring通过id找到这个对象；

一个bean标签声明一个对象。

**这种设置下，spring创建对象调用的是类的无参数构造器，所以如果要想让spring自动创建某个类的对象，该类必须有无参数构造器。后面会学到，添加标签和属性，spring可以调用有参构造器。**

class是对应类的全限定名称(不能是接口，只能是类)。

在spring框架中，有一个map，存放创建的对象，key和value分别是id和创建的对象。



例如有一个org.example.Test类，要让Spring去自动创建它，在配置文件beans.xml中应这样写：

`<bean id="test" class="org.example.Test" />`



## 创建容器对象ApplicationContext

spring帮我们创建好对象后，我们要使用ApplicationContext来获取对象，进行使用，它就表示spring容器。ApplicationContext是一个接口，最常用的实现类是ClassPathXmlApplicationContext，创建该类对象时有一个参数，是配置文件的路径。

```java
String config = "beans.xml";
ApplicationContext ac = new ClassPathXmlApplicationContext(config);
```

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201024172618382.png" alt="image-20201024172618382" style="zoom:50%;" />

这是例子项目的目录结构，beans.xml在resources目录下，在编译后，会在classes目录下，也就是类的根目录，所以变量config直接写"beans.xml"即可。

在创建ApplicationContext时，读取beans.xml文件时当读取到一个bean标签，就会创建一个对象。



然后使用该对象的getBean()方法来获取要使用的对象。该方法有多种重载形式，可以使用对象id作为参数来获取对象。并进行类型强制转换。

这种方法在后面慢慢就不会使用，但刚开始需要这样来获取。





## 使用容器对象获取对象信息

可以使用ApplicationContext的方法来获取对象信息

### 获取创建的对象数量

使用getBeanDefinitionCount()方法来获取，返回值是int类型

### 获取创建对象的名称(ID)

使用getBeanDefinitionNames()来获取，返回值是String数组





# 3-基于XML的DI实现

依赖注入(DI)：表示创建对象后，给对象的属性赋值。

DI有两种实现方法：

- 在spring配置文件中，使用标签和属性完成，叫做基于XML的DI实现。
- 使用spring的注解，完成属性赋值，叫做基于注解的DI实现。

DI的语法分类：

- set注入(设值注入)：spring调用类的set方法，来完成属性赋值
- 构造注入：spring调用类的有参数构造器，完成属性赋值。



先讲基于XML的DI实现。



创建一个User类：

```java
public class User {

    private String name;
    private Integer id;

    public void setName(String name) {
        this.name = name;
    }

    public void setId(Integer id) {
        this.id = id;
    }
}
```



## set注入(设值注入)

先来明确一个概念：简单类型。

Java 的简单类型包括基本数据类型和String。其他的都是引用数据类型。

使用XML配置文件进行设值注入的语法格式为：

上面是简单类型赋值，如果类里面包括引用类型，例如User里包括一个定义类Person，变量值为person，应这样定义：

```xml
<bean id="person" class="org.example.Person" />
<bean id="user" class="org.example.User" >
    <property name="person" ref="person"></property>
</bean>
```

也就是需要在配置文件中，定义一个Person对象，然后使用ref标签来指向这个对象。这个Person对象也可以在定义在User代码后面。



**注意：**

- **如果要使用设值注入，那么属性必须有对应的set方法。并且spring只是调用对应set方法，至于set方法里面语句，spring不关心。**

- **只要类里有set方法，那么就可以正常执行。也就是说，即使没有定义成员变量，只要有set方法就可以执行。**

    例如，User中没有定义email变量，但是有`public void setEmail(String email)`这个set方法，那么在配置文件中也可以正常去调用，不会报错。

- **赋值发生在调用无参数构造器创建对象之后。**



### bean一些其他属性



- ```xml
    <bean id="" class="" >
        <property name="属性名字" value="属性的值"></property>
    </bean>
    ```

    **其中bean标签还有一个属性为scope，它指定这个对象是单例还是多例，值分别为singleton/prototype**

    **当使用单例，那么每次ApplicationContext返回的都是同一个对象；如果使用多例，那么每次返回都是不同对象。**

    **并且，当使用单例，对象在创建IOC容器时创建；使用多例，对象在使用时才创建。**

- lazy-init属性：它只对singleton的对象有效，默认为false。即在IOC容器创建时创建该对象。如果指定为true，那么该对象会在使用它时才创建出来

- init-method和destory-method：想要在对象创建之后，执行某个方法，指定init-method属性；

    想要在IOC容器销毁后，执行某个方法，指定destory-method属性



## 构造注入

构造注入和设值注入语法差别不大。

语法为：

```xml
<bean id="" class="">
	<constructor-arg index="" name="" value=""></constructor-arg>
</bean>
```

参数解释：

- index：表示构造方法参数的位置，从左到右，从0开始计数
- name：构造函数的形参名
- value：如果是简单数据类型，赋值使用value
- ref：如果是引用数据类型，赋值使用ref

Index和name属性有一个就可以；两个可以都不写，但是标签的顺序需要和构造方法参数的顺序一致。







## 引用类型自动注入（对于引用类型赋值的简化）

在实际开发中，有大量的引用类型，如果每一个赋值都要用`<property-name>`标签，name代码会又臭又长。

引用类型的自动注入就是 spring框架可以根据某些规则自动给引用类型赋值，不再需要手动赋值了。

最常用的规则是byName、byType。

- byName(按名称注入)：**Java类中引用类型变量的名称 和 配置文件中<bean>标签的id名称一样，并且数据类型也一致，那么容器中的bean，就可以赋值给引用类型**

    语法：

    ```xml
    <bean id="" class="" autowire="byName">
    	简单类型属性赋值
    </bean>
    ```

    其中autowire属性，是指 引用类型按照 名称自动注入。

    例子：

    定义User：

    ```java
    public class User {
    
        private String name;
        private Integer id;
     	private Address address;//这个变量名称
     	
     	...
    }
    ```

    配置文件：

    ```xml
        <bean id="user" class="org.example.point2.User" autowire="byName" >
            <property name="id" value="21"/>
            <property name="name" value="Jack" />
        </bean>
    
        <bean id="address" class="org.example.point2.Address"><!--这个id名称-->
            <property name="addressId" value="1" />
            <property name="addressName" value="河北" />
        </bean>
    ```

    这个例子中，User对象并没有赋值address的语句，但是因为User中定义Address引用类型变量的名称和id="address"一样，所以会自动赋值给User的address变量。

    运行过程：spring在读取属性autowire后，知道按名字来注入，会先去User中拿到引用类型的名称address，然后和配置文件中所有bean的id比对，找到id一致的，并且数据类型相同，那么赋值给User的address引用变量。

    

- byType(按类型注入)：**Java类引用类型的数据类型和配置文件中bean标签的class属性是同源关系，这样的bean就可以赋值给引用类型**

    同源：

    - java类中引用类型和bean的class一致
    - java类中引用类型和bean的class是父子类关系
    - java类中引用类型和bean的class是接口和实现类关系

    语法：

    ```xml
    <bean id="" class="" autowire="byType">
    	简单类型赋值
    </bean>
    ```

    例子：

    ```xml
        <bean id="user" class="org.example.point2.User" autowire="byType" >
            <property name="id" value="21"/>
            <property name="name" value="Jack" />
        </bean>
    
        <bean id="Myaddress" class="org.example.point2.Address">
            <property name="addressId" value="1" />
            <property name="addressName" value="河北" />
        </bean>
    ```

    可以看出bean的id值并不是address，但是因为是按类型注入，所以也可以实现自动赋值。

    **注意：在按类型注入时，bean只能有一个符合条件的，多于一个就会出现错误。**

    

# 4-配置文件

在实际项目开发过程中，常使用多个配置文件。

多个配置文件的优势：

- 每个文件的大小比单独一个文件要小很多，读取保存快，效率高
- 避免多人协作，竞争带来的冲突。



配置文件分类有也很多种方式：

- 按模块分类，一个模块一个配置文件

- 按业务分类，例如数据访问层一个配置文件，服务层一个配置文件

    . . .

## 包含关系的多配置文件

当有多个配置文件时，可以使用一个总的配置文件来把这些配置文件都包含起来。

例如有student.xml和teacher.xml配置文件，那么可以有一个total.xml文件来把前两个配置文件包含在一起

语法为：

```xml
<beans>
	<import resource="其他配置文件的路径" />
</beans>
```

关键词："classpath:" 表示类路径，在spring的配置文件中要指定其他文件位置，需要使用classpath，告诉spring去哪里加载读取文件。

主配置文件中一般不包含bean，它只是用来包含其他配置文件的。

上面的例子可以这样写：

```xml
<beans>
	<import resource="classpath:student.xml"></import>
    <import resource="classpath:teacher.xml"></import>
</beans>
```

当然实际上，其他配置文件会在其他目录里，这里只是演示。



在使用ClassPathApplicationContext读取这个总配置文件时，就会将包含的两个配置文件一块读取。





### 使用通配符

在包含关系的配置文件中，可以使用通配符(*：表示任意字符)

例如有两个配置文件：spring-student.xml、spring-teacher.xml

那么在总配置文件中引入是可以这样写：
`<import resource="classpath:spring-*.xml" />`，这样就可以将两个配置文件都导入

**注意：**

- 总的配置文件名称不能包含在通配符的范围里面，否则会造成死循环。例如上面例子，总配置文件不能起名叫spring-*.xml
- 要使用通配符匹配的所有配置文件要在一个目录里，不同目录没法匹配



# 	5-基于注解的DI实现

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
2. 使用分隔符; 或 ,分割多个包名，例如`<context:component-scan base-package="org.example.package1;org.example.package2"`
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





# 6-动态代理AOP



## 简介

​		AOP(Aspect Orient Programming)，面向切面编程，是一种可以通过运行期间动态代理实现程序功能的统一维护的一种技术。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重写性，同时提高了开发效率。



​		具体来说，面向切面编程就是将交叉业务逻辑封装成切面，利用AOP容器的功能将切面植入到主业务逻辑中，交叉业务逻辑是指，通用的、与主业务逻辑无关的代码，例如日志，缓存，安全检查等。



​		AOP底层，采用的就是动态代理模式，采用了两种代理：jdk的动态代理，和CGLIB的动态代理。

​	    AOP实际就是动态代理的规范化，把动态代理的实现步骤、方式定义好，开发人员使用统一的方式实现动态代理。

### jdk动态代理

​		使用jdk的Proxy、Method和InvocationHandler创建代理对象，**jdk动态代理要求目标类必须实现接口。**

### CGLIB动态代理

​		是第三方的工具库，创建代理对象，原理是继承。**通过继承目标类，来创建子类，子类就是代理对象。**这种方式要求目标类不能是final，方法也不能是final。



## 切面

### 一些术语

1. **Aspect：切面，表示增强的功能。一般是一个类，也叫作切面类。完成一个非业务功能。常见有日志，事务，权限验证。**
2. JoinPoint：连接点，连接业务方法和切面的位置，就是目标类的业务方法。
3. **Pointcut：切入点，指多个连接点方法的集合，多个方法。**
    - **执行目标对象方法，动态的植入切面代码**
    - **通过切入点表达式，指定拦截哪些类的哪些方法，给指定的类在运行的时候植入切面类代码**
4. **切入点表达式：指定哪些类的哪些方法被拦截，即被植入切面类代码**
5. 目标对象：给哪个类增加功能，那么这个类就是目标对象
6. Advice：通知，通知表示切面功能指向的时间，在目标方法之前还是之后。

### 切面的三个关键要素

1. 切面的功能代码，也就是切面能干什么
2. 切面的执行位置，使用Pointcut来表示，也就是切面代码在哪个位置
3. 切面的执行时间，使用Advice表示时间，在目标方法之前还是之后。



## AOP的实现

AOP是动态代理的一个规范化。

AOP的技术实现框架：

1. spring：spring在内部实现了AOP规范，Spring主要在事务处理中使用AOP，在项目开发中很少使用Spring的AOP实现，因为比较笨重。
2. AspectJ：一个开源的框架，专门做AOP。Spring框架中集成了aspectj框架，通过spring就可以使用aspectj的功能。



aspectj框架实现AOP有两种方式：

- 使用XML的配置文件
- 使用注解，常用。





## AspectJ

### AspectJ中的几个常用类



#### JoinPoint接口

JoinPoint是Aspectj框架的一个类，表示切入点，它包含被执行的方法的一些信息，例如参数，方法修饰符等等。

在使用通知注解拦截目标方法后，可以在被注解方法签名中加上该参数，来获取被拦截的方法的一些信息。

常用方法：

- `Signature getSignature()`：获取封装了署名信息的对象，该对象可以获取到目标方法名，所属类的Class等信息
- `Object[] getArgs()`：获取传入目标方法的参数对象
- `Object getTarget()`：获取被代理的对象
- `Object getThis()`：获取代理对象

#### ProceedingJoinPoint接口

该接口是JoinPoint的子接口，只在环绕通知后的方法参数中使用。

除了上述方法，还有一个proceed的方法，用来执行目标方法。



### aspectj的切入点表达式

AspectJ定义了专门的表达式用于指定切入点，表达式的原型是：

​			`execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern)  throws-pattern?)`

​	解释：

- modifiers-pattern：访问权限类型
- **ret-type-pattern：返回值类型**
- declaring-type-pattern：包名类名
- **name-pattern(param-pattern)：方法名(参数类型和参数个数)**
- thorws-pattern：抛出异常类型



​	？代表可选的部分

​		加粗代表必须部分

用中文来表达就是：

`execution(访问权限  方法返回值   方法声明(参数) 异常类型)`

各部分使用空格分开，在其中可以使用以下符号

- *：0至多个任意字符
- .. ：用在方法参数中，表示任意多个参数；用在包名后，表示当前包及其子包路径
- +：用在类名后，表示当前类及其子类；用在接口后，表示当前接口及其实现类

例子：

- execution(public * *(..))：指定切入点为：任意的公共方法
- execution(* set*(..))：指定切入点为：任意一个以“set”开始的方法
- execution(* com.exy.service. * . *(..))：指定切入点为com.exy.service包中任意一个类的任意一个方法



### AspectJ通知注解

所有通知的方法都包含一个JoinPoint类型参数，就是一个切入点表达式。

#### @Before

​		在目标方法执行之前执行。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143540935.png" alt="image-20201129143540935" style="zoom:50%;" />



#### @AfterReturning

在目标方法执行之后执行，所以可以获取到目标方法的返回值。该注解的returning属性就是用于指定接收方法返回值的变量名的。所以被注解为后置通知的方法，除了可以包含JoinPoint参数(**该参数必须放在被注解方法的第一个参数位置**)外，还可以包含用于接收返回值的变量，该变量最好为Object类型。



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143712141.png" alt="image-20201129143712141" style="zoom:50%;" />



#### @Around

在目标方法之前或之后执行。**被注解为环绕增强方法要有返回值，推荐使用Object类型，**并且方法可以包含一个ProceedingJoinPoint类型的参数，接口ProceedingJoinPoint有一个proceed方法，用于执行目标方法，若目标方法有返回值，那么该方法的返回值就是目标方法返回值，最后环绕增强方法将返回值返回。该增强方法实际是拦截了目标方法的执行。



环绕通知实际就等同于是jdk的动态代理。



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143222572.png" alt="image-20201129143222572" style="zoom:43%;" />



因为目标方法是在被注解方法中，调用ProceedingJoinPoint的proceed方法执行的，所以被注解方法可以影响目标方法的执行，例如只在参数等于某个值或不等于某个值时执行。

例子：

doAround方法

```java
    @Override
    public String doAround(String name) {
        System.out.println("执行doAround方法,name为:" + name);
        return name;
    }
```



切面类中的方法

```java
	@Around(value = "execution(* doAround(..))")
    public Object myAround(ProceedingJoinPoint pjp) throws Throwable {
        Object[] args = pjp.getArgs();
        //判断传入参数是否为空，并且长度是否大于零
        if(args != null && args.length > 0) {
            String name = (String) args[0];
            //如果等于Jack，那么就执行目标方法，否则额拦截该方法
            if(name.equals("Jack")) {
                pjp.proceed();
            } else {
                System.out.println("方法被拦截，doAround方法没有执行!");
            }
        }
        return null;
    }
```

执行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201201094811143.png" alt="image-20201201094811143" style="zoom:33%;" />![image-20201201094837260](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201201094837260.png)



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201201094857381.png" alt="image-20201201094857381" style="zoom:33%;" />





#### @AfterThrowing

在目标方法抛出异常后执行，该注解的throwing属性用于指定所发生的的异常类对象，被注解为异常通知的方法可以包含JoinPoint参数和Throwable参数，参数名称为thorwing指定的名称，表示发生异常的对象。



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143432571.png" alt="image-20201129143432571" style="zoom:53%;" />

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143504626.png" alt="image-20201129143504626" style="zoom:50%;" />



#### @After

无论方法是否抛出异常，该增强方法都会执行。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143808737.png" alt="image-20201129143808737" style="zoom:50%;" />





### Pointcut

​		当较多的通知增强方法使用相同的execution切入点表达式时，编写、维护比较麻烦。Aspectj提供了@Pointcut注解，来定义execution切入点表达式。

​		用法是，将@Pointcut注解在一个方法之上，**以后所有的execution的value属性值都可以使用该方法名作为切入点**，代表的就是@Pointcut定义的切入点，使用@Pointcut注解的方法一般使用private标识，没有实际作用。

​		实际上使用@Pointcut，就相当于定义了一个变量，可以重复使用。



例如：

```java
@Pointcut(value = "execution(* *..service.*.*(..))")
private void mypointcut(){}
```

那么在之后使用切入点表达式就可以直接写`@After(value = "mypointcut()")`即可。



## 使⽤Spring AOP开发步骤(Aspectj)

### 引入AOP相关jar文件

- spring-aop--3.2.5.RELEASE.jar
- aopalliance.jar
- aspectjweaver.jar
- aspectjrt.jar

其实就是引入spring依赖和aspectj依赖。



### 注解⽅式实现AOP编程

我们之前⼿动的实现AOP编程是需要⾃⼰来编写代理⼯⼚的，现在有了Spring，就不需要我们⾃⼰写代 理⼯⼚了。Spring内部会帮我们创建代理⼯⼚。也就是说，不⽤我们⾃⼰写代理对象了。 因此，我们只要关⼼**切⾯类、切⼊点、编写切⼊表达式指定拦截什么⽅法就可以了！**



#### 创建切面类

​		其实就是普通类，需要在类上面加入`@Aspect`注解，来表明它是个切面类。

​		在类中定义方法，方法就是切面要执行的功能代码，在方法的上面加上aspectj的通知注解，例如`@Before`或者`@After`，并且需要指定切入点表达式execution()

​		其中在切面类中定义方法，该方法有限制：

- 必须是公共方法，public

- 方法没有返回值，@Around除外

- 方法名称自定义

- 方法可以有参数，也可以没有

    如果有，那么参数不是自定义的，有几个参数类型可以使用

    上面已经写了通知注解，除了切入点表达式参数。

    如果有其他参数，那么被注解的方法也要有相应参数，

    例如注解`@AfterReturning(value = "execution(* *(..))", returning="result")`，有一个returning参数，参数名为result，代表目标方法执行后的返回值，那么在被注解的方法签名中也要有参数`Object result`，参数名必须相同。

    其他例如异常通知也是这样。

    被注解方法本身还可以有JoinPoint参数。

    被注解的方法的参数不需要主动赋值，是aspectj框架自动赋值的。

#### 创建spirng配置文件

​		在配置文件中声明对象，把对象统一交给容器管理。

​		声明对象可以用注解，也可以用XML配置。



​		并且声明aspectj框架中的自动代理生成器标签`<aop:aspectj-autoproxy>`，这个标签是用来完成代理对象的自动创建功能的。

​		工作原理是，`<aop:aspectj-autoproxy>`通过扫描找到@Aspect定义的切面类，再由切面类根据切入点找到目标类的目标方法，再由通知类型找到切入的时间点。



**注意：如果找不到对应的目标方法，那么就不会把代理代码植入到目标类的目标方法中，执行的就是原来代码。**



#### 使用ApplicationContext获取目标对象

这里的目标对象就是经过aspectj修改后的代理对象，也就是目标类对象。







###  一个例子

​		使用注解完成容器管理对象。



先创建接口有一个doSome方法，实现这个接口创建一个目标类，实现doSome方法，然后再创建切面类，实现在执行doSome方法之前，输出当前时间的功能。



创建接口：

```java
public interface SomeService {
    void doSome(String name, Integer age);
}
```

创建接口实现类：

```java
@Component("someService")
public class SomeServiceImpl implements SomeService {
    @Override
    public void doSome(String name, Integer age) {
        System.out.println("名字为 :" +name + ", 年龄为：" + age);
    }
}
```

创建切面类：

```java
@Aspect
@Component("myAspect")
public class MyAspect {

    @Before("execution(public void doSome(String, Integer))")
    public void myBefore() {
        System.out.println("切面功能，输出执行时间：" + new Date());
    }
}
```



测试代码：

```java
    @Test
    public void Test01() {
        String config = "applicationContext.xml";
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(config);
        SomeService proxy = (SomeService) applicationContext.getBean("someService");
        proxy.doSome("Jack", 21);
    }
```

运行结果

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129145426769.png" alt="image-20201129145426769" style="zoom:50%;" />





### 注意

如果目标类有接口，那么Spring框架默认会使用jdk的动态代理，如果没有接口，那么spring会使用CGLIB代理。

但是如果希望在有接口的情况下，让Spring使用CGLIB动态代理，就需要在自动代理生成器标签中添加一个属性，`proxy-target-class = "true"`



即`<aop:aspectj-autoproxy  proxy-target-class="true" />`

就可以让Spring默认使用CGLIB代理。









# 7-Spring集成MyBatis

## MyBatis使用步骤



先来回顾以下MyBatis的使用步骤：

1. 创建DAO接口
2. 创建实体类
3. 创建映射文件
4. 创建MyBatis主配置文件
5. 使用SqlSessionFactory创建出SqlSession对象(也在主配置文件中)
6. 使用SqlSession对象获得dao接口的实现类对象
7. 执行数据库操作



## 集成到Spring后需要的改动



那么将MyBatis集成到Spring中以后。需要改动的有：

- 主配置文件中不再需要数据源的配置，数据源要交给Spring容器来管理，在spring配置文件中配置。
- 对mapper文件的注册，应该使用<package />标签，即只需要给出mapper映射文件所在的包。因为mapper文件的名称和DAO接口名称相同。因此使用这种方式的好处是，若有多个映射文件，配置也不需要修改。当然也可以使用原来的<resource />标签
- SqlSessionFactory对象不需要我们来创建，也交给spring容器。
- DAO对象同样交给spring容器。

因此，需要让spring创建的对象有：

- 独立的连接池类对象，不使用MyBatis默认的连接池，而使用阿里的Druid连接池
- SqlSessionFactory对象
- dao对象





**注意：MyBatis集成到Spring后，默认是自动提交事务，不需要写`sqlSession.comit();`。**

## MyBAtis集成到Spring后使用步骤

1. 新建Maven项目

2. 加入maven依赖

    - spring依赖
    - mybatis依赖
    - mybatis和spring集成的依赖
    - mysql驱动依赖
    - spring事务的依赖

3. 创建实体类

4. 创建dao接口

5. 创建mapper文件

6. 创建myBatis主配置文件

7. 创建Service接口和实现类，实现类成员变量是dao对象

8. 创建spring配置文件：声明将mybatis的对象交给spring创建，有：

    - 数据源
    - SqlSessionFactory
    - DAO对象
    - 自定义的Service对象

    

9. 编写测试代码，获取Service对象，通过service调用dao来操作数据库

     





## 配置数据源

首先在spring配置文件applicationContext.xml文件中配置数据源，查询druid的github官网，可以看到通用配置：

```xml
 <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close"> 
     <property name="url" value="${jdbc_url}" />
     <property name="username" value="${jdbc_user}" />
     <property name="password" value="${jdbc_password}" />

     <property name="filters" value="stat" />

     <property name="maxActive" value="20" />
     <property name="initialSize" value="1" />
     <property name="maxWait" value="60000" />
     <property name="minIdle" value="1" />

     <property name="timeBetweenEvictionRunsMillis" value="60000" />
     <property name="minEvictableIdleTimeMillis" value="300000" />

     <property name="testWhileIdle" value="true" />
     <property name="testOnBorrow" value="false" />
     <property name="testOnReturn" value="false" />

     <property name="poolPreparedStatements" value="true" />
     <property name="maxOpenPreparedStatements" value="20" />

     <property name="asyncInit" value="true" />
 </bean>
```

其中属性 `init-method="init"`和`destory-method="close"`是固定的，这两个方法是在DruidDataSource中写好的。

在上面的配置中，通常只需要配置url、username、password和maxActive。

其中maxActive是设置最大有多少连接数。

Druid会自动根据url识别驱动类名，所以不需要配置driver。





## 创建SqlSessionFactory对象

在spring文件中配置SqlSessionFactory对象。

之前创建SqlSessionFactory对象，只需要MyBatis配置文件，但是现在把数据源的配置挪到了Spring配置文件中，所以需要两部分，一是Spring配置的数据源，二是mybatis配置文件。

格式为：

```xml
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean" >
        <property name="dataSource" ref="" />
        <property name="configLocation" value="classpath:" />
     </bean>
```

其中，第一个<property>的ref属性是上面配置数据源的id

第二个<property>标签，是指定mybatis配置文件路径的。**但是需要使用value属性，并且要加上`classpath:`**

在后面写上mybatis的配置文件路径。





## 创建DAO接口对象

将创建dao接口对象也交给Spring容器，在配置文件中进行配置。

传统创建DAO对象，需要使用SqlSession对象，调用它的getMapper()方法，并把接口的类作为参数传给该方法。

那么在配置时，同样需要这几项。

使用MapperScannerConfigurer类：它会在内部多次调用getMapper生成多个dao接口的代理对象

语法格式为：

```xml
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer" >
        <property name="sqlSessionFactoryBeanName" value="" />
        <property name="basePackage" value="" />
    </bean>
```

**该bean不需要id属性。**

其中，第一个<property>标签， 指定的是SqlSessionFactory，值应该是之前创建的SqlSessionFactory的id

第二个<property>标签，指定的是DAO接口所在的包名，MapperScannerConfigurer会扫描这个包的每个接口，调用getMapper方法创建每个接口的代理对象，也就是DAO对象。



**创建的dao对象的名字是接口名的首字母小写。**



## 创建Service接口和实现类

在service包下，创建表的service接口。并在service.impl包下，创建对应实现类。在实现类里定义dao接口对象成员变量，然后不同方法对应数据库的操作。



## 一个例子

首先创建实体类Student：

```java
public class Student {
    private Integer id;
    private String name;
    private String email;
    private Integer age;
    //构造器
    //set和get方法
}    
```



在dao包中创建DAO接口StudentDao

```java
public interface StudentDao {

    Integer insertStudent(Student student);
    List<Student> selectStudent();
}
```

创建映射文件：

```xml
<mapper namespace="org.example.dao.StudentDao">
    
    <insert id="insertStudent">
        insert into student values (#{id}, #{name}, #{email}, #{age})
    </insert>

    <select id="selectStudent" resultType="org.example.domain.Student">
        select * from student
    </select>
</mapper>
```

创建MyBatis主配置文件：

```xml
<configuration>
    <!--设置-->
    <settings>
        <!--输出日志到控制台-->
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
    
    <!--设置别名-->
    <typeAliases>
        <package name="org.example.domain"/>
    </typeAliases>
    
    <!--映射文件配置-->
    <mappers>
        <package name="org.example.dao"/>
    </mappers>

</configuration>
```



创建service接口和实现类：

```java
public interface StudentService {

    Integer addStudent(Student student);
    List<Student> selectStudents();
}
```

```java
public class StudentServiceImpl implements StudentService {

    private StudentDao studentDao;

    public void setStudentDao(StudentDao studentDao) {
        this.studentDao = studentDao;
    }

    @Override
    public Integer addStudent(Student student) {
        return studentDao.insertStudent(student);
    }

    @Override
    public List<Student> selectStudents() {
        List<Student> list = studentDao.selectStudent();
        return list;
    }
}
```



创建spring配置文件applicationContext.xml

```xml
    <!--配置数据源-->
	<bean id="druid" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="jdbc:mysql://localhost:3306/test" />
        <property name="username" value="root" />
        <property name="password" value="*******" />
        <property name="maxActive" value="20" />
    </bean>
	<!--创建SqlSessionFactory对象-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean" >
        <property name="dataSource" ref="druid" />
        <property name="configLocation" value="classpath:mybatis.xml" />
     </bean>
	<!--创建dao接口代理对象-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer" >
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
        <property name="basePackage" value="org.example.dao" />
    </bean>

    <bean id="studentService" class="org.example.service.impl.StudentServiceImpl" >
        <property name="studentDao" ref="studentDao" />
    </bean>
```

编写测试代码：

```java
    @Test
    public void testSelectStudents() {
        String config = "applicationContext.xml";
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(config);
        StudentService studentService =(StudentService) applicationContext.getBean("studentService");

        List<Student> list = studentService.selectStudents();
        for(Student stu: list) {
            System.out.println(stu);
        }
    }
    
    @Test
    public void testAddStudent() {
        String config = "applicationContext.xml";
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(config);
        StudentService studentService =(StudentService) applicationContext.getBean("studentService");
        Student stu = new Student(1012, "林冲", "linchong@qq.com", 35);
        int num = studentService.addStudent(stu);
        System.out.println("影响行数为：" + num);
    }
```



执行查询操作，运行结果为：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201204155239728.png" alt="image-20201204155239728" style="zoom:50%;" />



执行插入操作，运行结果为：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201204155317439.png" alt="image-20201204155317439" style="zoom:50%;" />





## 使用属性文件

使用properties属性文件来配置数据库连接信息，在spring配置文件中引用配置文件。

首先加入在spring配置文件中加入命名空间：

```xml
xmlns:context="http://www.springframework.org/schema/context"
```

然后使用`<context:property-placeholder location="classpath:" />`

标签来设置属性文件的路径

最后通过`${属性文件中设置的key}`来进行访问



例子：

设置路径：

```xml
<context:property-placeholder location="classpath:jdbc.properties" />
```

使用：

```xml
    <bean id="druid" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.username}" />
        <property name="password" value="${jdbc.password}" />
        <property name="maxActive" value="${jdbc.max}" />
    </bean>
```









# 8-Spring事务

## 概念

事务最开始是数据库中的概念，在DAO层。数据库中的事务指一组sql语句的集合，我们希望这些语句都成功或者都失败。

即执行是一致的。当涉及多个表或者多个sql语句的insert，update等，需要保证这些语句都成功，可以使用事务。

事务有四个特性：

- 原子性：指事务不可分割，是一个整体

- 一致性：事务中的语句要么都执行，要么都不执行。保持一致

- 隔离性：该事务的执行不影响其他事务的执行

- 持久性：事务执行完以后，永久有效。

但一般情况，需要将事务提升到业务层，即Service层。这样做是为了能够使用事务的特性来管理具体的业务。

Spring中，通常使用两种方式实现对事务的管理：

- 使用Spring的事务注解管理事务
- 使用AspectJ的AOP配置管理事务



事务处理应放在Service的业务方法上，因为业务方法要运行多个sql语句。



## 之前数据库事务处理方式的不足

- 不同数据库访问技术，处理事务的对象、方法不同，JDBC和MyBatis、Hibernate的事务处理各不相同。
- 需要了解不同数据库访问技术使用事务的原理
- 掌握多种数据库事务的处理逻辑，什么时候提交事务，什么时候回滚事务
- 处理事务的多种方法

大致总结就是：多种数据库的访问技术，有不同的事务处理的机制、对象和方法。

那么解决不足的方法就是：使用Spring提供的一种处理事务的统一模型，使用统一步骤、方法完成对不同数据库访问技术的事务处理。





## Spring事务管理



Spring使用事务管理器来管理事务。事务管理器是一个接口，它有众多的实现类。

接口：PlatformTransactionManager,定义了事务方法commit、rollback

实现类：spring把每一种数据库访问技术对应的事务处理类都创建好了。

DateSourceTransactionManager类：使用JDBC或MyBatis进行数据库操作

HibernateTransactionManager类：使用Hibernate进行持久化数据时使用



使用：我们需要告诉spring使用的是哪种数据库访问技术，就是在配置文件中使用<bean>标签声明，创建数据库访问技术对应的类。



### Spring回滚方式

Spring事务的默认的回滚方式是：发生运行时异常和error时回滚，发生编译异常时提交。但是，对于编译异常，我们可以手动设置回滚方式。

复习一下错误和异常：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201206131613291.png" alt="image-20201206131613291" style="zoom:67%;" />



### 事务定义接口

在创建好<bean>标签后，还需要说明事务的类型。

事务定义接口TransactionDefinition中定义了事务描述相关的三类常量：事务隔离级别、事务传播行为和事务默认超时时限，及对它们的操作。

#### 事务隔离级别

事务的并发问题：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201206102734602.png" alt="image-20201206102734602" style="zoom:50%;" />

隔离级别就是用来处理这些问题。



可取五个值，但实际只有四种情况(这四种情况由上到下隔离级别越来越高)：

- DEFAULT：采用数据库默认事务隔离级别，MySQL默认为REPEATABLE_READ
- READ_UNCOMMITED：读未提交，**一个事务可以读取另一个未提交事务的数据**，未解决任何并发问题
- READ_COMMITED：读已提交，**一个事务要等到另一个事务提交后，才能读取事务。**解决读脏数据、存在不可重复读和幻读
- REPEATABLE_READ：可重复读，**就是在开始读取数据后，不允许修改数据。**解决读脏数据、不可重复读，存在幻读
- SERIALIZABLE：串行化，**即事务串行化顺序执行**，不存在并发问题。但是这种级别效率很低，很耗数据库性能，一般不使用



#### 事务超时时间

表示一个方法最长的执行时间，如果方法执行超过了该时间，事务就回滚。单位是秒，整数。默认为-1.

一般不去管它，使用默认值即可。



#### 事务传播行为

事务传播行为是指：处在不同事务的方法在相互调用时，执行期间事务的维护情况。例如：A事务中的方法doSome()调用B事务中的方法doOther()，在调用执行期间事务的维护情况，就是事务传播行为。事务传播行为行为是加在方法上的。

事务传播行为常量都是以PROPAGATION_开头，一共有其中情况

- **PROPAGATION_REQUIRED**
- **PROPAGATION_REQUIRES_NEW**
- **PROPAGATION_SUPPORTS**
- PROPAGATION_MANDATORY
- PROPAGATION_NESTED
- PROPAGATION_NEVER
- PROPAGATION_NOT_SUPPORTED

其中前三个最为常用，只需掌握前三个即可。



##### **PROPAGATION_REQUIRED**

指定的方法必须在事务内执行，若当前存在事务，就加入到当前事务；若当前没有事务，则必须创建一个事务，然后加入到新创建的事务中。**总而言之，指定了该传播行为的方法，必须在事务内执行。**



例如：如果该传播行为加在A方法上，在B方法中调用A方法。如果，B方法是在事务内执行的，那么A方法就加入到该事务；如果B方法没有在事务内执行，那么在A方法内会创建一个事务，并在其中执行。



##### **PROPAGATION_SUPPORTS**

指定的方法支持当前事务，但如果没有事务，也可以以非事务方式执行。也就是说，指定了该传播行为的方法，如果执行时有事务，就在事务内执行，如果没有，同样可以在无事务下运行。



##### **PROPAGATION_REQUIRES_NEW**

总是新建一个事务，如果当前存在事务，就将该事务挂起，直到新事务执行完毕。



### Spring事务管理使用

管理事务的是事务管理器接口和它的实现类。

使用时，有以下几个步骤：

- 指定要使用的事务管理器实现类，使用<bean>标签
- 指定哪些类的哪些方法需要加入事务的功能
- 指定隔离级别、传播行为和超时时间



### 使用注解管理事务

通过使用@Transactional注解方式，该注解是spring框架提供的，可以将事务植入到相应public方法中，实现事务管理。

@Transactional注解的可选属性如下：

- propagation：用于设置事务传播属性，属性类型为Propagation枚举。默认值为PROPAGATION_REQUIRED
- isolation：用于设置事务隔离级别，属性类型为Isolation枚举。默认值为ISLLATION_DEFAULT
- readOnly：用于设置该方法对于数据库操作是否是只读的。属性为boolean，默认值为false。当查询操作时，可设为true
- timeout：设置本操作与数据库连接的超时时限。单位为秒，类型为int，默认值为-1，即没有时限。
- rollbackFor：指定需要回滚的异常类。即方法抛出这些异常类，就需要回滚。类型为Class[]，默认值为空数组。当只有一个异常类时，可以不使用数组。
- rollbackForClassName：指定需要回滚的异常类类名，类型为String[]，默认值为空数组。当只有一个异常类时，可以不使用数组

- noRollbackFor：指定不需要回滚的异常类，即抛出这些异常时，不需要回滚。类型为Class[]，默认值为空数组。当只有一个异常类时，可以不使用数组。
- noRollbackForClassName：指定需要不回滚的异常类类名，类型为String[]，默认值为空数组。当只有一个异常类时，可以不使用数组

**注意：**

**@Transactional注解**

- **若用在方法上，只能用在public方法上。对于非public方法，如果加上@Transactional注解，spring不会报错，但不会将指定事务植入到该方法。因为Spring会忽略掉所有非public方法的@Transactional注解。**

- **若@Transactional注解用在类上，则表示该类的所有public方法都植入事务。**

- rollbackFor：表示发生指定的异常一定回滚

    处理机制是：

    1. spring框架会首先检查抛出的异常是否在rollbackFor的属性值中，如果在，那么不管是什么类型异常，一定回滚
    2. 如果不在rollbackFor中，那么spring会判断异常是不是RuntimeException，如果是一定回滚。

    **所以，rollbackFor其实只管编译时异常，而不需要管运行时异常。因为抛出运行时异常一定回滚。**



#### 使用步骤

1. 在配置文件中声明事务管理器，就是使用<bean>标签声明事务管理器对象，属性dataSource就是创建的数据源

    ```xml
        <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager" >
            <property name="dataSource" ref="dataSourcce" />
        </bean>
    ```

    

2. 开启注解驱动，就是告诉spring框架，要使用注解的方式管理事务。

    Spring使用AOP机制，创建@Transactional所在类的代理对象，给方法加入事务的功能。

    在业务方法执行之前，开启事务，业务方法之后，提交或回滚事务，使用的是AOP的环绕通知。

    ```xml
    <tx:annotation-driven transaction-manager="transactionManager" />
    ```

    注意：这里有四个同名的annotation-driven对应的命名空间，应该选择这个

    ```xml
    xmlns:tx="http://www.springframework.org/schema/tx"
    ```

    

3. 业务层public方法加上事务属性，即在方法上加上@Transactional注解

    ```xml
        @Transactional(
                propagation = Propagation.REQUIRED,
                isolation = Isolation.DEFAULT,
                readOnly = false,
                rollbackFor = {...}
        )
    ```

    可以这样写，也可以直接写`@Transactional`，不加括号里的内容。则代表使用默认值，rollbackFor默认抛出运行时异常时回滚事务。

## 一个完整的例子

### 创建两个表

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201206130953994.png" alt="image-20201206130953994" style="zoom:50%;" />

### 加入pom依赖

```xml
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.1</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.3.1</version>
    </dependency>
    <dependency>
      <groupId>javax.annotation</groupId>
      <artifactId>javax.annotation-api</artifactId>
      <version>1.3.2</version>
    </dependency>
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.6</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>5.3.1</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.3.1</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>5.3.1</version>
    </dependency>
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>2.0.6</version>
    </dependency>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.21</version>
    </dependency>
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.2.3</version>
    </dependency>
  </dependencies>
```

### 创建实体类

两个实体类，Goods和Sale

Goods：

```java
public class Goods {

    private Integer id;
    private String name;
    private Integer amount;
    private Float price;
    //构造器
    //set和get方法
}    
```

Sale：

```java
public class Sale {

    private Integer id;
    private Integer gid;
    private Integer nums;
    //构造器
    //set和get方法
}    
```

### 创建接口

同样有两个，操作Goods的GoodsDao接口，操作Sale的SaleDao接口。

GoodsDao：

```java
public interface GoodsDao {

    int updateGoods(Goods goods);
    Goods selectGoods(Integer goodsId);
}
```

SaleDao：

```java
public interface SaleDao {

    int insertSale(Sale sale);
}
```

### 创建映射文件

一个接口一个映射文件

GoodsDao.xml：

```xml
<mapper namespace="org.example.dao.GoodsDao">


    <update id="updateGoods">
        update goods  set amount = amount - #{amount} where id=#{id}
    </update>
    <select id="selectGoods" resultType="org.example.domain.Goods">
        select * from goods where id = #{goodsId}
    </select>
</mapper>
```

SaleDao.xml：

```xml
<mapper namespace="org.example.dao.SaleDao">

    <insert id="insertSale">
        insert into sale(gid, nums) values (#{gid},#{nums})
    </insert>

</mapper>
```

### 自定义异常类

定义一个库存不足抛出的异常类NotEnoughException：

```java
public class NotEnoughException extends RuntimeException {

    public NotEnoughException() {
        super();
    }

    public NotEnoughException(String message) {
        super(message);
    }
}
```

### 创建Service接口和实现类

接口中只定义一个buy方法，代表买商品，需要操作两个表，在Sale中加入销售记录，更新Goods表中对应商品库存。

```java
public interface BuyGoodsService {
    /**
     *
     * @param goodsId:购买商品的编号
     * @param nums:购买商品的数量
     */
    void buy(Integer goodsId, Integer nums);
}
```

实现类：

```java
public class BuyGoodsServiceImpl implements BuyGoodsService {

    private GoodsDao goodsDao;
    private SaleDao saleDao;

    public void setGoodsDao(GoodsDao goodsDao) {
        this.goodsDao = goodsDao;
    }

    public void setSaleDao(SaleDao saleDao) {
        this.saleDao = saleDao;
    }


    //增加事务注解，也可以直接加上@Transactional，效果一样
    @Transactional(
            propagation = Propagation.REQUIRED,
            isolation = Isolation.DEFAULT,
            readOnly = false,
            rollbackFor = {NullPointerException.class, NotEnoughException.class}
    )
    @Override
    //这个方法先插入销售记录，而不是先判断存不存在该商品和商品库存，是为了更明显的显示事务回滚和提交
    //如果没有该注解，那么当抛出异常，销售记录依然存在
    //加上注解，抛出了异常，事务回滚，销售记录会删除
    public void buy(Integer goodsId, Integer nums) {
        Sale sale = new Sale();
        sale.setGid(goodsId);
        sale.setNums(nums);
        saleDao.insertSale(sale);
        Goods goods = goodsDao.selectGoods(goodsId);
        if(goods == null) {
            throw new NullPointerException("没有该商品");
        } else if(goods.getAmount() < nums) {
            throw new NotEnoughException("商品库存不足");
        }

        Goods buyGoods = new Goods();
        buyGoods.setId(goodsId);
        buyGoods.setAmount(nums);
        goodsDao.updateGoods(buyGoods);


    }
}
```



### 编写spring配置文件

省略了mybatis配置文件和属性文件，因为非常简单。

spring配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.alibaba.com/schema/stat http://www.alibaba.com/schema/stat.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">


    <context:property-placeholder location="classpath:jdbc.properties" />

    <bean id="druid" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.username}" />
        <property name="password" value="${jdbc.password}" />
        <property name="maxActive" value="${jdbc.maxActive}" />
    </bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean" >
        <property name="dataSource" ref="druid" />
        <property name="configLocation" value="classpath:mybatis.xml" />
    </bean>
    
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer" >
        <property name="basePackage" value="org.example.dao" />
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
    </bean>

    <bean id="buyGoodsService" class="org.example.Service.impl.BuyGoodsServiceImpl" >
        <property name="goodsDao" ref="goodsDao" />
        <property name="saleDao" ref="saleDao" />
    </bean>

    <!--创建事务管理器对象-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager" >
        <property name="dataSource" ref="druid" />
    </bean>
    
	<!--事务驱动-->
    <tx:annotation-driven transaction-manager="transactionManager" />


</beans>
```



### 编写测试类

只测试抛出异常的情况。抛出两个异常，事务都会回滚，情况差不多，只测试一个

#### 抛出NotEnoughException异常

```java
    @Test
    public void testBuyGoodsService() {
        String config = "applicationContext.xml";
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(config);
        BuyGoodsService buyGoodsService = (BuyGoodsService) applicationContext.getBean("buyGoodsService");
        buyGoodsService.buy(1001, 10000);

    }
```

库存不够，运行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201206132520782.png" alt="image-20201206132520782" style="zoom:50%;" />

同时Sale表和Goods表并没有发生变化：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201206132559732.png" alt="image-20201206132559732" style="zoom:50%;" />

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201206132607728.png" alt="image-20201206132607728" style="zoom:50%;" />













## 使用AspectJ的AOP配置管理事务

使用注解配置事务代理方式的不足是，当有很多类、很多方法需要配置时，需要大量的配置事务，非常麻烦。

这时候，使用@AspectJ的AOP来进行配置。在spring配置文件中声明类、方法需要的事务，使用这种方法，业务逻辑和事务配置完全分离。

适合大型项目。前面那一种适合中小型项目。



### 使用步骤



### 声明事务管理器对象

只要有事务管理，就要声明事务管理器对象，这个和前面是一样的。

```xml
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager" >
        <property name="dataSource" ref="dataSource" />
    </bean>
```



### 配置事务属性

因为不使用注解来配置事务，就需要在配置文件中进行配置。

使用`<tx:advice>`标签，事务通知来进行配置

语法格式为：

```xml
    <tx:advice id="" transaction-manager="transactionManager" >
        <tx:attributes>
            <tx:method name="" propagation="" isolation="" read-only="" rollback-for=""/>
        </tx:attributes>
    </tx:advice>
```

其中

`<tx:advice>`的id属性用来标识该标签，是一个自定义的唯一值。

transaction-manager属性是事务管理器的id。

`<tx:attributes>`中有`<tx:method>`标签，是用来给具体的方法配置事务，可以有多个，分别给不同方法设置事务属性。

`<tx:method>`中：

- name：方法名称，值有两种类型
    1. 完整的方法名称，不带包和类
    2. 使用通配符*表示任意字符
- propagation：事务传播行为
- isolation：事务隔离级别
- read-only：是否只读
- no-rollback-for：回滚异常类，值是异常类的全限定类名，多个类用 `,`分开



注意：如果使用通配符配置方法名称，应该设置同一类的方法名称有共同的单词，便于通配符设置。



### 配置AOP

配置好不同方法事务属性后，可以发现我们并没有指定是哪个包哪个类的方法。就是说，如果有多个类都有同一个方法，应该使用哪个方法。

那么下面就需要配置AOP，来指定

语法格式：

```xml
    <aop:config>
        <aop:pointcut id="" expression="execution()"/>
        <aop:advisor advice-ref="" pointcut-ref="" />
    </aop:config>
```

其中：
**标签`<aop:pointcut>`用来设置切入点，id用来唯一标识该切入点，expression属性就是切入点表达式。用来指定哪些包的哪些类使用事务。**

**标签`<aop:advisor>`用来将前面的事务通知标签`<tx:advice>`和切入点pointcut标签连接起来。两个属性值都是id值**





### 例子

在之前的例子基础上，修改一下

#### 配置事务管理器对象

```xml
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager" >
        <property name="dataSource" ref="druid" />
    </bean>
```

#### 配置事务通知

````xml
    <tx:advice id="advice" transaction-manager="transactionManager" >
        <tx:attributes>
            <tx:method name="*" />
        </tx:attributes>
    </tx:advice>
````

#### 配置AOP

```xml
    <aop:config>
        <aop:pointcut id="service" expression="execution(* *.*.service..*.*(..))"/>
        <aop:advisor advice-ref="advice" pointcut-ref="service" />
    </aop:config>
```

切入点表达式代表，service之前有两个包，service之后的`..`代表service包和子包







# 9-Spring和Web

​		在Web项目中使用Spring框架，首先要在web层(这里指Servlet)获取到Spring容器对象。只要获取到了Spring容器，就可以从该容器中获取到Service对象。

​		那么接下来就要思考在哪里创建Spring容器。

​		对于一个web应用来说，Spring容器对象只需要一个就可以了。所以很显然不能直接在Servlet中创建Spring容器，因为虽然Servlet是单例多线程，但多个Servlet都需要用到Spring容器，就会创建多个Spring容器。

​		我们想到，对于一个web应用，全局作用域对象ServletContext也只有一个，所以可以在创建全局作用域对象的时候创建Spring容器，并把Spring容器对象存入ServletContext中。

​		之前学到了全局作用域的监听器接口ServletContextListener，我们可以使用监听器监听全局作用域，当全局作用域创建的时候同时创建Spring容器，并放入全局作用域对象中。

​		可以自定义全局作用域监听器接口实现类，也可以使用spring的一个模块spring-web提供的实现类ContextLoaderListener。



## 使用步骤

### 加入依赖

首先加入spring-web依赖

```xml
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>5.3.1</version>
    </dependency>
```

### 在web配置文件web.xml中注册监听器

```xml
    <listener-class>
      org.springframework.web.context.ContextLoader
    </listener-class>
  </listener>
```



### 在web配置文件web.xml文件中指定spring配置文件路径

使用<context-param>标签来配置，语法格式为：

```xml
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring配置文件路径</param-value>
  </context-param>
```

子标签<param-name>的值就是contextConfigLocation，固定不变。<param-value>就是spring配置文件的路径。

ContextLoaderListener会从ServletContext中获取该参数，然后创建spring容器。

注意，如果从ServletContext中获取web.xml初始参数，需要使用的方法是`getInitParameter(String name)`，而不是`getAttribute(String name)`。



如果没有使用<context-param>标签指定spring配置文件路径，那么会默认使用WEB-INF/applicationContext.xml这个路径。



#### 获取Spring容器对象

获取Spring容器对象有两种方法。

在之前的例子中，Spring容器对应类时ApplicationContext，在web应用中容器对应类时WebApplicationContext，它是ApplicationContext的子类。



#### 从ServletContext中获取

Spring容器对象在ServletContext中存放的key为`WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE。`

可以通过这个key，调用ServletContext的`getAttribute(String name)`方法获取WebApplicationContext。

```java
String attr = "WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE";
WebApplicationContext ac = (WebApplicationContext) this.getServletContext().getAttribute(attr);
```



#### 通过WebApplicationContextUtils获取

WebApplicationContextUtils是一个工具类，它有一个`getRequiredWebApplicationContext(ServletContext sc)`方法，传入ServletContext参数，返回值就是Spring容器对象。

```java
WebApplicationContext wac = WebApplicationContextUtils.getRequiredWebApplicationContext(sc);
```







# 10-Spring常用注解

## @Configuration

位置：一般用在类上

作用：表示该类作为一个配置类，配置一些内容



## @Bean

位置：一般用在方法上，**并且该方法所在类是配置类，即被@Configuration注解的类**

作用：表示把该方法返回的对象交给Spring容器管理



## @Service

位置：用在类上

作用：标注业务层组件



## @Controller

位置：用在类上

作用：标注控制层组件



## @Repository

位置：用在类上

作用：标注数据访问层组件，即DAO组件



## @Compinent

位置：用在类上

作用：泛指组件，当组件不好归类，就用该注解进行标注



## @Primary

位置：用在类上

作用：当自动装配出现多个Bean候选者时，被注解@Primary的Bean将作为首选项。



## @Autowired

位置：用在变量上

作用：默认按类型装配，当出现多个Bean，则再按名称，如还是有多个Bean，并且没有@Primary，那么就会出现错误



## @Resource

位置：用在变量上

作用：默认按名称装配



