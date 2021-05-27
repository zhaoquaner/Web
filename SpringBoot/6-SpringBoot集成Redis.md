# 6-SpringBoot集成Redis

集成Redis很简单。

1. 添加依赖
2. 在application.properties中配置redis
3. 使用RedisTemplate类操作Redis

## 添加依赖

首先添加SpringBoot的Redis起步依赖。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

commons-pools是一个连接池，用来管理Redis连接对象。

## 配置Redis

在application.properties中配置Redis：

```properties
## 是否启动日志SQL语句
spring.jpa.show-sql=true
 
# Redis 数据库索引（默认为 0）
spring.redis.database=0
spring.redis.host=localhost
spring.redis.port=6379
# Redis 服务器连接密码（默认为空）
spring.redis.password=
# springboot 2.0 redis默认客户端已换成lettuce
 
# 连接池最大连接数（使用负值表示没有限制） 默认 8
spring.redis.lettuce.pool.max-active=8
# 连接池最大阻塞等待时间（使用负值表示没有限制） 默认 -1
spring.redis.lettuce.pool.max-wait=-1
# 连接池中的最大空闲连接 默认 8
spring.redis.lettuce.pool.max-idle=8
# 连接池中的最小空闲连接 默认 0
spring.redis.lettuce.pool.min-idle=0
spring.redis.timeout=5000
```

分别是要连接的Redis服务器，端口号， 和密码(没有密码可以不写此项)。



## RedisTemplate



redisTemplate在存取数据时，会进行序列化和反序列化。就是把要存取的Java对象序列化和反序列化。

有几种序列器：

- JdkSerializationRedisSerializer：POJO对象的存取场景，使用JDK本身序列化机制，将pojo类通过ObjectInputStream/ObjectOutputStream进行序列化操作，最终redis-server中将存储字节序列。是默认的序列化策略。

- StringRedisSerializer：Key或者value为字符串的场景，根据指定的charset对数据的字节序列编码成string，是“new String(bytes, charset)”和“string.getBytes(charset)”的直接封装。是最轻量级和高效的策略。

- Jackson2JsonRedisSerializer：jackson-json工具提供了javabean与json之间的转换能力，可以将pojo实例序列化成json格式存储在redis中，也可以将json格式的数据转换成pojo实例。因为jackson工具在序列化和反序列化时，需要明确指定Class类型，因此此策略封装起来稍微复杂。【需要jackson-mapper-asl工具支持】

- GenericFastJsonRedisSerializer：另一种javabean与json之间的转换，同时也需要指定Class类型。

- OxmSerializer：提供了将javabean与xml之间的转换能力，目前可用的三方支持包括jaxb，apache-xmlbeans；redis存储的数据将是xml工具。不过使用此策略，编程将会有些难度，而且效率最低；不建议使用。【需要spring-oxm模块的支持】



通过使用这几种序列化方式，可以自定义想如何序列化。

例如想让key的value使用Json序列化方式，就可以设置Jackson2JsonRedisSerializer或GenericFastJsonRedisSerializer。



注意使用Jackson时，记得导入依赖：

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.datatype</groupId>
    <artifactId>jackson-datatype-jsr310</artifactId>
</dependency>
```



可以不创建Redis配置类，使用SpringBoot默认的配置，也可以创建Redis配置类，自定义如何序列化、反序列化等配置。

## 使用默认RedisTemplate操作Redis

在服务类中使用@Resource或者@Autowired自动注入RedisTemplate对象。

该类时用来操作Redis的。

SpringBoot会自动配置RedisTemplate。





举个例子(例子中，RedisTemplate在控制器类中自动注入，但实际开发一般都是在服务层Service类中自动注入。这里只是为了演示)。

代码：

```java
    @Resource
    private RedisTemplate<String, String> redisTemplate;


    @PostMapping(value = "/put")
    public Object putRedis(String key, String value) {
        redisTemplate.opsForValue().set(key, value);
        return "成功插入数据,key:" + key + ", value:" + value;
    }

    @GetMapping(value = "/get")
    public Object getRedis(String key) {
        return "key:" + key + "对应的value为:" +
                redisTemplate.opsForValue().get(key);
    }

```

一个是插入数据，一个是获取数据。









可以使实体类实现Serializable序列化接口。然后就可以将Java对象存入到Redis中。

例如：
实体类Student：

```java
public class Student implements Serializable {
        private Integer id;

    private String name;

    private String email;

    private Integer age;
    //set和get方法
}
```



处理器类：

```java
    @Autowired
    private StudentService studentService;

    @Resource
    private RedisTemplate<String, Object> redisTemplate;    


	@GetMapping(value = "/get/{id}")
    public Object getRedis(@PathVariable("id") Integer id) throws IOException {
        Student student = studentService.queryStudentById(id);
        redisTemplate.opsForValue().set("student1", student);
        return redisTemplate.opsForValue().get("student1");

    }
```

SpringBoot提供的Redis依赖有默认的序列化工具和反序列化。

处理器返回的就是一个Stuent对象。





## 自定义配置

创建配置类RedisConfig。

注意，这时候使用自动注入，变量名要写返回RedisTemplate对象的方法名，这里使用的是redis。

```java
@Configuration
@EnableCaching //开启注解
public class RedisConfig extends CachingConfigurerSupport {

    /**
     * retemplate相关配置
     * @param factory
     * @return
     */
    @Bean
    public RedisTemplate<String, Object> redis(RedisConnectionFactory factory) {

        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 配置连接工厂
        template.setConnectionFactory(factory);

        //使用Jackson2JsonRedisSerializer来序列化和反序列化redis的value值（默认使用JDK的序列化方式）
        Jackson2JsonRedisSerializer jacksonSeial = new Jackson2JsonRedisSerializer(Object.class);

        ObjectMapper om = new ObjectMapper();
        // 指定要序列化的域，field,get和set,以及修饰符范围，ANY是都有包括private和public
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);

        //使用Jackson2Json无法正确反序列LocalDateTime，需要手动添加模块
        om.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS);
        om.registerModule(new JavaTimeModule());

        // 指定序列化输入的类型，类必须是非final修饰的，final修饰的类，比如String,Integer等会跑出异常
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jacksonSeial.setObjectMapper(om);

        // 值采用json序列化
        template.setValueSerializer(jacksonSeial);
        //使用StringRedisSerializer来序列化和反序列化redis的key值
        template.setKeySerializer(new StringRedisSerializer());

        // 设置hash key 和value序列化模式
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setHashValueSerializer(jacksonSeial);

        // 设置支持事务
        template.setEnableTransactionSupport(true);
        template.afterPropertiesSet();

        return template;
    }	

    /**
     * 对hash类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public HashOperations<String, String, Object> hashOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForHash();
    }

    /**
     * 对redis字符串类型数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public ValueOperations<String, Object> valueOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForValue();
    }

    /**
     * 对链表类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public ListOperations<String, Object> listOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForList();
    }

    /**
     * 对无序集合类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public SetOperations<String, Object> setOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForSet();
    }

    /**
     * 对有序集合类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public ZSetOperations<String, Object> zSetOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForZSet();
    }

}
```





注意：反序列化LocalDateTime时，Jackson2是无法反序列的，需要手动添加模块。详细看上面代码。





