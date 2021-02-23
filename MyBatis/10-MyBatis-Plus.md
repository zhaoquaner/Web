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





## 查询操作

`BaseMapper`有几种查询方法：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210221130459868.png" alt="image-20210221130459868" style="zoom:67%;" />



挨个介绍：

- `List<T> selectBatchIds(Collection<? extends Serializable>)`：以Id为查询条件做批量查询，即可同时查询多个Id匹配的行，参数是一个集合，可以是List、Set和Queue，返回值是一个List集合

    例子：

    ```java
        @Test
        public void testSelect() {
            List<Student> students = studentMapper.selectBatchIds(Arrays.asList(1001, 1002, 1003));
            students.forEach(System.out::println);
        }
    ```

    结果：

    ​	![image-20210221131546875](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210221131546875.png)

- `T selectById(Serializable)`：单个查询，返回值是对应实体类

    例子：

    ```java
        @Test
        public void testSelect() {
            Student student = studentMapper.selectById(1001);
            System.out.println(student);
        }
    ```

    运行结果：

    <img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210221131708309.png" alt="image-20210221131708309" style="zoom:150%;" />

- `List<T> selectByMap(Map<String, Object>)`：条件查询，使用Map对象，key是String，代表属性名；Object代表属性值。多个key/value对，使用and连接。

    例子：

    ```java
        @Test
        public void testSelect() {
            HashMap map = new HashMap();
            map.put("name", "刘备");
            List<Student> students = studentMapper.selectByMap(map);
            students.forEach(System.out::println);
        }
    ```

    

    运行结果：

    ![image-20210221132145371](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210221132145371.png)

    

    例子：

    ```java
        @Test
        public void testSelect() {
            HashMap<String, Object> map = new HashMap<>();
            map.put("name", "刘备");
            map.put("age", "76");
            List<Student> students = studentMapper.selectByMap(map);
            students.forEach(System.out::println);
        }
    ```

    运行结果：

    ![image-20210221132308173](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210221132308173.png)





后面七个方法都使用了Wrapper条件构造类来设定查询条件。后面再讲。





## 删除操作

首先来讲一下逻辑删除和物理删除。

### 逻辑删除

逻辑删除的本质就是更新或者修改操作。它并不是真的把数据删除。而是在表中将对应的是否删除标识(如deleted属性或is_delete属性)，做修改操作。例如0是未删除，1是删除。如果该字段修改为1，那么在逻辑上数据是被删除的，但是数据依然存在在数据库中。

### 物理删除

与之对应的就是物理删除，就是实实在在的删除。数据不会存在在数据库中。



这两种删除的一个应用就是回收站。在将文件加入回收站时，并不会删除，而只是把对应删除标识修改，从回收站恢复该文件，则再修改标识，这是逻辑删除；而清空回收站就是物理删除。



### MyBatis-Plus的删除操作

几个方法：

- `int delete(Wrapper<T>)`：使用Wrapper构造条件删除
- `deleteById(Serializable)`：根据主键删除
- `deleteByMap(Map<String, Object>)`：根据Map集合删除
- `deleteBatchIds(Collection<? extends Serializable>)`：根据集合删除

用法基本都和查询操作差不多。不多讲。

重点讲一下如何实现逻辑删除。

### 实现逻辑删除

1. 在表中添加deleted字段，类型为boolean或int，默认值是false或0，不为空。

2. 在配置文件中配置：

    ```properties
    mybatis-plus.global-config.db-config.logic-not-delete-value=0
    mybatis-plus.global-config.db-config.logic-delete-value=1
    ```

3. 在实体类deleted属性上添加注解`@TableLogic`

    ```java
        @TableLogic
        private Boolean deleted;
    ```

    就可以了。

实现了逻辑删除后，MyBatis-Plus一系列CRUD操作会发生改变：

- 插入操作：不作限制，推荐字段在数据库设置默认值
- 查询操作：追加where条件来过滤已删除数据，如`where deleted = 0`
- 更新操作：追加where条件防止更新已删除数据
- 删除操作：转变为更新操作，即`update tablename set deleted = 1 where ...`



字段类型支持说明：

- 支持所有数据类型，推荐使用Integer、Boolean和LocalDateTime
- 如果数据库字段使用datetime，逻辑未删除值和已删除值支持配置为字段串`null`，另一个值致辞配置为函数来获取值如`now()`

例子：

删除操作：

```java
    @Test
    public void testDelete() {
        int i = studentMapper.deleteById(1044);
        System.out.println(i);
        System.out.println(i == 1 ? "删除成功" : "删除失败");
    }
```

结果：

![image-20210221140256573](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210221140256573.png)



查询操作：

```java
    @Test
    public void selectAll() {
        List<Student> students = studentMapper.selectList(null);
        students.forEach(System.out::println);
    }
```

结果：

![image-20210221140352545](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210221140352545.png)





## 条件构造器

MyBatis-Plus有一个条件构造抽象类Wrapper，它是用来构造复杂的where条件的。

Wrapper有几个子类：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210221141448914.png" alt="image-20210221141448914" style="zoom:80%;" />



主要就分为查询和更新对应Wrapper类。



### AbstractWrapper



首先介绍抽象类AbstractWrapper有的方法：

注意：

- 出现的方法中第一个形参condition表示该条件是否加入最后生成的sql中
- 在形参中出现的R为泛型，在普通wrapper中是String，在LanbdaWrapper中是函数
- 以下方法出现的`R column`均表示数据库字段，而不是实体类数据库字段名。
- 使用中如果形参的Map或List为空，则不会加入最后生成的sql中



为了方便，不会再写含有形参condition的方法。condition形参永远放在第一个参数位置。

方法：

- `allEq(Map<R, V> params)`<br>`allEq(Map<R, V> params, boolean null2IsNull)`<br>

    说明：

    全部相等(或个别isNull)<br>`params`：key为数据库字段名，value为字段值<br>`null2IsNull`：为true则在map的value为空时调用`isNull`方法，false则忽略vaue为空的字段

    示例：

    `allEq({id:1, name:"Jack", age :null})`-->`id = 1 and name = Jack and age is null`

    `allEq({id:1, name:"Jack", age :null}, false)`-->`id = 1 and name = Jack`

- ------

    `between(R column, Object val1, Object val2)`

    `notBetween(R column, Object val1, Object val2)`

    说明：前两个表示指定字段值在设定两个值之间，闭区间，即`[2,6]`；后两个方法表示不在两个设定值之间，开区间，即($-\infty$, 2) & (6, $+\infty$)

    示例：

    `between("age", 18 ,30)`-->`age between 18 and 30`

    `notBetween("age", 18 ,30)`-->`age not between 18 and 30`

    ---

    

- `like(R column Object val)`

    `notLike(R column, Object val)`

    说明：Like '%值val%'；NotLike '%值val%'

    示例：

    `like("name", 'k')`-->`name like '%k%'`

    `notLike("name", 'k')`-->`name not like '%k%'`

    

    根据`%`位置，还有`likeLeft(R column, Object val)`和`likeRight(R column, Object val)`两个方法。

    分别对应`%`放在左边和右边。

    ---

- `isNull(R column)`

    `isNotNull(R column)`

    说明：

    - 第一个判断字段为空
    - 第二个判断字段不为空

    示例：
    `isNull("name")`-->`name is null`

    `isNotNull("name")`-->`name is not null`

    ---

- `is(R column, Collection<?> value)`

    `is(R column, Object... values)`

      

    `notIn(R column, Collection<?> value)`

    `notIn(R column, Object... values)`

    说明：

    - 前两个使用in关键词
    - 后两个使用not in关键词

    示例：

    `in("age", {1, 2, ,3})`-->`age in (1, 2, 3)`

    `in("age", 1, 2, 3)`-->`age in (1, 2, 3)`

    `Notin`和`in`用法一样

    ---

-  `nested(Consumer<Param> consumer)`

    表示正常嵌套，只加括号，不带and或or

    例如：`nested(i->i.eq("name", 'Jack').ne("status", 1))`-->`(name = 'Jack' and status = 1)`

    
    
    `apply(String applySql, Object... params)`
    
    拼接sql
    
    注意：该方法可用于数据库函数动态入参的params对应前面的applySql内部的index部分，这样不会有sql注入风险，反之会有。
    
    例如：`apply("id = 1")`-->`id = 1`
    
    ​			`apply("date_format(dataColumn, '%Y-%m-%d') = '2008-08-08'")`-->`date_format(dataColumn, '%Y-%m-%d') = '2008-08-08'`
    
    ---
    
- `eq(R column, Object val)`

    说明：等于=

    示例：

    `eq("name"， "赵四")`-->`name = '赵四'`



与eq类似的还有：

- ne：not equal，不相等
- gt：greater than，大于
- ge：greater equal，大于等于
- lt：less than，小于
- le：小于等于

还有一些简单的方法，看名字就知道作用和用法：

- `orderByDesc`
- `orderByAsc`
- `groupBy`
- `having`
- `esists`
- `notExists`



还有两个方法：

- `or()`
- `and()`

表示使用or或and拼接。

注意：主动调用`or`表示下一个方法使用or拼接。不调用则默认使用and。

`or(Consumer<Param> consumer)`

表示or嵌套。

例如：

`or(i -> i.eq("name", 'Jack').ne("status", 1))`-->`or (name = 'Jack' and status <> 1)`



###  QueryWrapper

`select(String... sqlSelect)`

用于设置查询字段。

例如：

`select("id", "name", "age")`



### UpdateWrapper

有两个方法：



- `set(String column, Object val)`

    SQL的set字段

    例如`set("name", "Jack")`

    `set("name", "")`-->字段值变为空字符串

    `set("name", null)`-->字段值变为null

- `setSql(String sql)`

    直接设置set部分的sql语句

    如：`setSql("name='Jack'")`







## 代码生成器

使用代码生成器自动生成代码。



```java
public class DataAutoGenerator {


    public static void main(String[] args) {
        //创建自动生成器对象
        AutoGenerator ag = new AutoGenerator();
        
        //创建全局配置对象
        GlobalConfig gc = new GlobalConfig();
        //获取项目绝对路径
        String projectPath = System.getProperty("user.dir");
        //设置输出目录
        gc.setOutputDir(projectPath + "/12-MyBatis_Plus/src/main/java");
        //设置作者
        gc.setAuthor("zhao-xin");
        //设置生成后不打开输出目录
        gc.setOpen(false);
        //设置Service类命名
        gc.setServiceName("%sService");
        //开启 swagger2 模式
        gc.setSwagger2(true);
        //添加全局配置到自动生成器
        ag.setGlobalConfig(gc);

        //创建数据源对象，并配置数据源。这部分比较熟悉，不细讲了。
        DataSourceConfig dsf = new DataSourceConfig();
        dsf.setUrl("jdbc:mysql://localhost:3306/test");
        dsf.setDriverName("com.mysql.cj.jdbc.Driver");
        dsf.setUsername("root");
        dsf.setPassword("bigbang2022");
        dsf.setDbType(DbType.MYSQL);
        ag.setDataSource(dsf);

        //配置包
        PackageConfig pc = new PackageConfig();
        //配置父包，entity等包生成在该包中
        pc.setParent("com.example");
        //设置实体类，数据访问层，服务层和控制层的名称
        pc.setEntity("entity");
        pc.setMapper("mapper");
        pc.setService("service");
        pc.setController("controller");
        //添加到自动生成器中
        ag.setPackageInfo(pc);



        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        //设置命名策略为下划线转驼峰
        strategy.setNaming(NamingStrategy.underline_to_camel);
        //设置列名命名策略也为下划线转驼峰
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        //是否开启实体类使用lombok
        strategy.setEntityLombokModel(true);
        //使用Rest风格
        strategy.setRestControllerStyle(true);

        //设置需要生成的表名
        strategy.setInclude("student");
        
        strategy.setControllerMappingHyphenStyle(true);
        
        //设置逻辑删除字段
        strategy.setLogicDeleteFieldName("deleted");
        //设置乐观锁字段
        strategy.setVersionFieldName("version");

        //设置自动填充字段
        List<TableFill> list = new ArrayList<>();
        list.add(new TableFill("create_time", FieldFill.INSERT));
        list.add(new TableFill("update_time", FieldFill.INSERT_UPDATE));
        strategy.setTableFillList(list);
        //添加到自动生成器对象
        ag.setStrategy(strategy);

        //设置模板引擎
        ag.setTemplateEngine(new VelocityTemplateEngine());
        
        //执行
        ag.execute();


    }
}

```







```java
52.74.223.119 github.com
199.232.69.194 github.global.ssl.fastly.net
140.82.114.4 gist.github.com
185.199.108.153 assets-cdn.github.com
199.232.96.133 raw.githubusercontent.com
199.232.96.133 gist.githubusercontent.com
199.232.96.133 cloud.githubusercontent.com
199.232.96.133 camo.githubusercontent.com
199.232.96.133 avatars0.githubusercontent.com
199.232.96.133 avatars1.githubusercontent.com
199.232.96.133 avatars2.githubusercontent.com
199.232.96.133 avatars3.githubusercontent.com
199.232.96.133 avatars4.githubusercontent.com
199.232.96.133 avatars5.githubusercontent.com
199.232.96.133 avatars6.githubusercontent.com
199.232.96.133 avatars7.githubusercontent.com
199.232.96.133 avatars8.githubusercontent.com
```



































