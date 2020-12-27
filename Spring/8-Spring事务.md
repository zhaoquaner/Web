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

