# 10-MyBatisPlus

​	

## 简介

MyBatis-Plus是MyBatis的增强工具。在MyBatis上，只做增强，不做改变。目的是简化开发，提高效率。

### 特性

1. 无侵入：就是只做增强，不做改变。不会影响现有工程
2. 损耗小：启动会自动注入基本的CRUD，性能基本无损耗
3. 强大的CRUD操作：内置了通用的Mapper，通用的Service。也有强大的条件构造器
4. 支持Lambda形式调用：使用Lambda表达式编写各类查询条件
5. 支持主键自动生成：支持四种主键策略
6. 内置代码生成器：采用代码或者Maven插件，可以直接生成Mapper、Model、Service、Controlloer代码
7. 内置分页插件
8. 支持多种数据库
9. 内置性能分析插件
10. 内置全局拦截插件



## 一个简单的例子

1. 首先创建一张表，或者使用现成的表也可以。

使用的表：student
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205113108645.png" alt="image-20210205113108645" style="zoom:67%;" />

2. 创建一个SpringBoot项目

3. 添加mysql依赖，mybatis-plus依赖

    ```xml
            <dependency>
                <groupId>com.baomidou</groupId>
                <artifactId>mybatis-plus-boot-starter</artifactId>
                <version>3.4.2</version>
            </dependency>
    ```

4. 在`application.properties`配置mysql

    <img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205113510725.png" alt="image-20210205113510725" style="zoom:50%;" />

5. 在包entity中创建实体类Student

    ```java
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    public class Student {
        private Integer id;
        private String name;
        private String email;
        private String age;
    }
    ```

    **注意：**

    **实体类名字一定要和表名一致，大小写不区分。**

    **属性名和表中列名要保持一致，表中列名尽量使用`_`符号。代表前后是两个单词，MyBatis-Plus会把后面一个单词第一个字母大写，并去掉`_`例如列名`user_name`，会对应`userName`。**

6. 在包dao中创建StudentMapper接口，继承MyBatis-Plus提供的通用Mapper接口`BaseMapper<T>`，泛型就是对应的实体类。加上注解`@Mapper`。

    ```java
    @Mapper
    public interface StudentMapper extends BaseMapper<Student> {
    }
    ```

    这样就写完了基本的CRUD操作了。这些方法都由`BaseMapper`写好了。

7. 测试

    ```java
        @Autowired
        private StudentMapper studentMapper;
    	@Test
        public void testMybatisPlus() {
            List<Student> students = studentMapper.selectList(null);
            students.forEach(System.out::println);
    
        }
    ```

    测试结果：

    <img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205113948108.png" alt="image-20210205113948108" style="zoom:67%;" />



可以看到，使用MyBatis-Plus，代码量很少。不需要写SQL语句，xml文件。



## 配置日志输出

在`application.properties`配置文件中配置数据库日志输出

`mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl`。

代表标准输出。



## 主键生成策略

当进行插入操作时，如果没有指定主键。但主键又不能为空。所有这时候就需要生成主键。

### `@TableId`

使用注解`@TableId`来标记一个表的注解。该注解应放在实体类中对应主键的属性上。

该注解有两个属性：

- value：字段名，可有可无。默认是空字符串
- type：类型

需要设置的就是type类型，值是一个枚举类型：

- `IdType.AUTO`：表示表中主键是自增ID，使用该值，则表的主键必须是自增ID，否则没用
- `IdType.NONE`：为设置主键类型，等于跟随全局，约等于INPUT
- `IdType.INOUT`：表示需要手动输入，如果没输入，就设为null
- `IdType.ASSIGN_ID`：自动分配ID，使用雪花算法(主键类型为number或string)
- `IdType.ASSIGN_UUID`：自动分配ID，使用UUID算法

如不指定，默认使用`IdType.NONE`。

还有三种``IdType.ID_WORDKER`、`IdType.UUID`、`IdType.ID_WORKER_STR`，已经被抛弃不使用了。

前三种无需介绍，详细介绍后两种。

### 雪花算法

SnowFlake 算法，是 Twitter 开源的分布式 id 生成算法。其核心思想就是：使用一个 64 bit 的 **long** 型的数字作为全局唯一 id。在分布式系统中的应用十分广泛，且ID 引入了时间戳，基本上保持自增的。

这64位中，分为4部分：

1. 占一位：固定是0
2. 占41位：表示时间戳，单位毫秒。存储的是(当前时间戳-开始时间戳)，开始时间戳一般是id生成器开始使用的时间，由程序指定，41位，可以用69年。也就是从开始时间一直用69年，才会用完所有数字
3. 占10位：表示程序所在机器信息。也表示该服务最多可部署在$2^{10}$台机器上。其中5个bit代表机房id，5个bit代表机器id
4. 占12位：用来记录同一毫秒产生的不同id，即，如果前面所有都一样，这部分用来区分不同id。占12位，表示一毫秒可生产$2^{12}$个不同的id，即4096个id

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205161158740.png" alt="image-20210205161158740" style="zoom:70%;" />



### UUID

不过多介绍。

UUID有16个字节，通常以36字节字符串表示。

UUID有多个版本，版本不同，实现的方式不同。





## 更新操作

​	`BaseMapper<T>`接口提供了两个更新操作相关方法。

**<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205165816947.png" alt="image-20210205165816947" style="zoom:80%;" />**



第一个方法先不讲。

第二个方法，参数对应实体类对象。

### 动态SQL

在更新操作时，传入一个实体类对象，MyBatis-Plus会动态拼接SQL语句，当属性值不为空，则更新该属性；当属性值为null，就不会更新。

例子：

```java
    @Test
    public void testUpdate() {
        Student student = new Student();
        student.setId(1013);
        student.setAge(43);
        int i = studentMapper.updateById(student);
        System.out.println(i);
        System.out.println(i == 1 ? "更新成功": "更新失败");
    }
```

只设置了主键ID和年龄。日志：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205170259420.png" alt="image-20210205170259420" style="zoom:80%;" />



可以看到，SQL语句只更新了age。

加上`student.setEmail("wusong@qq.com")`

再次执行效果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205170406889.png" alt="image-20210205170406889" style="zoom:67%;" />



可以看到，已经加上了email字段。

所以MyBatis-Plus会动态拼接SQL语句。

### 自动填充字段

有时候，一些字段，不应该手动传值更新，例如创建时间，修改时间。这些应该要自动地进行修改。

MyBatis-Plus提供了这样的功能。

#### `TableField`

该注解用于非主键的属性。

该注解有以下几个属性：

| 属性               | 类型                           | 说明                                                         | 默认值                    | 必须指定 |
| ------------------ | ------------------------------ | ------------------------------------------------------------ | ------------------------- | -------- |
| `value`            | `String`                       | 数据库字段名                                                 | `""`                      |          |
| `el`               | `String`                       | 映射为原生`#{..}`逻辑，<br>相当于xml文件里的`#{...}`部分     | `""`                      |          |
| `exist`            | `Boolean`                      | 是否为数据库表字段                                           | `true`                    |          |
| `condition`        | `String`                       | 字段`where`实体查询比较的条件<br>有值按设置的值为准，没有则默认全局 | `""`                      |          |
| `update`           | `String`                       | 字段`update set`部分注入，<br>例如`update="%s+1"`，表示更新时会<br>set version = version + 1 | `""`                      |          |
| `insertStrategy`   | `Enum`                         | 自动插入字段时的插入策略                                     | `DEFAULT`                 |          |
| `updateStrategy`   | `Enum`                         | 自动更新字段时的更新策略                                     | `DEFAULT`                 |          |
| `whereStrategy`    | `Enum`                         | 测试该字段是否为null或空字符串                               | `DEFAULT`                 |          |
| **`fill`**         | `Enum`                         | 字段自动填充策略                                             | `FieldFill.DEFAULT`       |          |
| `select`           | `boolean`                      | 是否进行select查询                                           | `true`                    |          |
| `keepGlobalFormat` | `boolean`                      | 是否保持使用全局format进行处理                               | `false`                   |          |
| `jdbcType`         | `jdbcType`                     | JDBC类型                                                     | `jdbcType.UNDEFINED`      |          |
| `typeHandler`      | `Class<? extends TypeHandler>` | 类型处理器                                                   | `UnknownTypeHandler.clss` |          |
| `numericScale`     | `String`                       | 指定小数点后保留位数                                         | `""`                      |          |

其中需要使用的是`fill`属性。

共有4种值：

- `FieldFill.DEFAULT`：不处理，默认值
- `FieldFill.INSERT`：插入时填充字段
- `FieldFill.UPDATE`：更新时填充字段
- `FieldFill.INSERT_UPDATE`：插入和更新时填充字段

如果只想在添加一行数据时填充指定字段，更新时不再填充，例如`create_time`创建时间，那么就使用`FieldFill.INSERT`

而想在添加和更新数据时都填充指定字段，使用`FieldFill.INSERT_UPDATE`。



可以实现`MetaObjectHandler`接口来实现自定义填充策略。

实现两个方法：

- `void insertFill(MetaObject metaObject)`：插入策略，fill属性使用了`FieldFill.INSERT`或`FieldFill.INSERT_UPDATE`策略的会调用该方法
- `void updateFill(MetaObject metaObject)`：更新策略，fill属性使用了`FieldFill.UPDATE`或`FieldFill.INSERT_UPDATE`策略会调用该方法

`MetaObject`代表元数据。

并在两个方法中调用`this.setFieldValByName(String fieldName, Object val, MetaObject metaObject);`

其中：

- fieldName：代表属性名称，例如要更新`createTime`字段，那么这个值就是`createTime`
- val：属性值，例如更新`createTime`字段，那么就可以赋值`new Date()`
- metaObject：传入方法参数的那个metaObject即可



注意：

- 填充的原理就是直接给实体类对应属性赋值
- MetaObjectHandler提供的默认策略是：如果属性有值则不覆盖，填充值为null则不填充
- 自定义填充处理器需要加上`@Component`注解，来交给Spring容器管理
- 如需要输出日志，则加上注解`@Slf4j`

#### 例子

首先在实体类中添加两个属性，并加上注解`@TableField`：

```java
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
```



首先创建一个填充处理类：

```java
@Slf4j
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {

    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("插入填充开始");
        this.setFieldValByName("createTime", new Date(), metaObject);
        this.setFieldValByName("updateTime", new Date(), metaObject);
        log.info("插入填充结束");
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("更新填充开始");
        this.setFieldValByName("updateTime", new Date(), metaObject);
        log.info("更新填充结束");
    }
}
```

进行插入操作：

```java
    @Test
    public void testInsert() {
        Student student = new Student();
        student.setId(1014);
        student.setName("如来佛祖");
        student.setAge(1000);
        student.setEmail("rulai@163.com");
        studentMapper.insert(student);
    }
```

注意：这里并没有写`createTime`和`upateTime`

运行结果：

![image-20210205173912472](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205173912472.png)



并且打印出自定义的日志：

![image-20210205173953002](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205173953002.png)



进行更新操作：

```java
    @Test
    public void testUpdate() {
        Student student = new Student();
        student.setId(1014);
        student.setAge(1200);
        int i = studentMapper.updateById(student);
        System.out.println(i);
        System.out.println(i == 1 ? "更新成功": "更新失败");
    }
```

运行结果：

![image-20210205174143487](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205174143487.png)

打印出自定义日志：

![image-20210205174202105](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210205174202105.png)





### 乐观锁和悲观锁

#### 并发控制介绍

并发控制：当程序可能出现并发的情况时，就需要保证在并发状态下数据的准确性，以确保当前用户和其他用户一起操作时，得到的结果和单独操作时的结果是一样的，这种手段就是并发控制。

简单来说：并发控制就是保证一个用户的工作不会对另一个用户的工作产生不合理的影响。

如果没有做好并发控制，就会导致**脏读**、**幻读和不可重复读**等问题。



首先明确：乐观锁和悲观锁，都是定义出来的概念，是一种思想。

**乐观锁用于读多写少的场景，悲观锁用于写多读少的情况。**



#### 悲观锁

介绍：

​	当对数据库数据进行修改时，为了避免同时被其他人修改，最好的办法是直接对该数据进行加锁以防止并发。这种借助数据库锁机制，在修改数据之前先锁定，再修改的方式称为悲观并发控制。

​	悲观锁，具有独占和排他特性，之所以叫悲观锁，是因为它总是假设最坏的情况，每次读取数据时都默认其他线程会更改数据。

悲观锁的实现：

- 传统关系型数据库使用这种锁机制，例如行锁，表锁，读锁，写锁等，都是在操作前先上锁
- Java中的同步`synchronized`关键字

悲观锁主要分为**共享锁**和**排它锁**：

- 共享锁又称为读锁，简称S锁。共享锁是多个事务对于统一数据可以共享一把锁，都可以访问数据，但不能修改只能读
- 排它锁称为写锁，简称X锁。排它锁不能与其他锁并存，如果一个事务获取了一个数据的排它锁，那么其他事务就不能再获取该数据的其他锁，包括共享锁和排它锁。获取排它锁的事务可以对数据进行读取和修改

说明：

​		悲观并发控制实际是“先取锁再访问”的保守策略，为数据处理安全提供了保证。但是在效率方面，处理加锁的机制会让数据库产生额外开销，也增加了产生死锁的机会，另外会降低并行性。

​	

#### 乐观锁(Optimistic Locking)

介绍：

​		乐观锁是相对悲观锁而言，乐观锁加上数据一般不会发生冲突，所以在数据进行提交更新时，才会正式对数据的冲突进行检测，如果冲突，则返回错误的信息，让用户决定如何去做。乐观锁适合读操作较多的场景。

乐观锁的实现：

- CAS实现
- 版本号控制：也是一会要介绍的。一般是在数据库表或你某个字段加上一个数据版本号version字段，表示数据被修改的次数。当数据被修改时，version值会+ 1，当线程A要更新数据值时，读取数据同时也读取version值，在提交更新时，若刚才读取的version和当前数据库version值一致才更新，否则重试更新操作，直到更新成功



## 插件

MyBatis-Plus提供的插件都需要在配置类中注册组件。

首先创建配置类，并加上`@Configuration`，并创建一个方法：

```java
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return interceptor;
    }
```

MyBatisPlusInterceptor是MyBatis-Plus提供的拦截器类，在方法中新建一个类，需要什么插件，就创建对应的插件接口实现类，并使用`addInnerInterceptor()`添加到拦截器中。

可用的插件接口实现类有：

- `PaginationInnerInterceptor`：自动分页
- `TenanLineInnerInterceptor`：多租户
- `DynamicTableNameInnerInterceptor`：动态表名
- `OptimisticLockerInnerInterceptor`：乐观锁
- `IllegalSQLInnerInterceptor`：sql性能规范
- `BlockAttackInnerInterceptor`：防止全表更新和删除



注：也可以在该配置类中加上注解`@MapperScan`，不加其实也行，因为DAO接口加上注解`@Mapper`，不需要扫描。

### MyBatis-Plus实现乐观锁

MyBatis-Plus实现乐观锁非常简单。

在数据库表中加上一个字段`version`，可以是int类型，并设默认值为1

只要在实体类中添加定义一个属性`private Integer version`，并在其上加上注解`@Version`：

```java
    @Version
    private Integer version;
```

注意：

- 支持的数据类型只有：int、Integer、long、Long、Date、Timestamp、LocalDateTime
- 整数类型下 `newVersion = oldVersion + 1`
- `newVersion`会写回到实体类中
- 只支持`updateById()`和`update(entity, wrapper)`方法
- 在`update(entity, wrapper)`方法中，wrapper不能复用

**特别注意！注意！：只有在更新之前，进行查询操作，才会实现乐观锁，单纯的更新不会实现。**

例如：

```java
    @Test
    public void testUpdate() {
        Student student = new Student();
        student.setId(1014);
        student.setAge(34);
        int i = studentMapper.updateById(student);
        System.out.println(i);
        System.out.println(i == 1 ? "更新成功": "更新失败");
    }
```

如果这样写，是不会实现乐观锁的。因为获取不到version值。

**必须要在更新之前进行查询操作：**

```java
    @Test
    public void testUpdate() {
        Student student = studentMapper.selectById(1013);
        student.setAge(34);
        int i = studentMapper.updateById(student);
        System.out.println(i);
        System.out.println(i == 1 ? "更新成功": "更新失败");
    }
```

这样才会实现乐观锁。



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210206180311794.png" alt="image-20210206180311794" style="zoom:80%;" />



### 总结

1. 自定义填充处理类不要忘记加注解`@Component`
2. 实体类属性上记得加注解`TableField`，并指定`fill`属性
3. 实现乐观锁，一定要先进行查询操作。气死，TM弄了一天。





