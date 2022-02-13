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
