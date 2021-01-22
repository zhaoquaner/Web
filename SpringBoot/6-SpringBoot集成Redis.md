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
```

## 配置Redis

在application.properties中配置Redis：

```properties
spring.redis.host=
spring.redis.port=6379
spring.redis.password=
```

分别是要连接的Redis服务器，端口号， 和密码(没有密码可以不写此项)。

## 使用RedisTemplate操作Redis

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

