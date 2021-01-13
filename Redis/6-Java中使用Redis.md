# 6-Java中使用Redis

在Java中使用Redis。可以使用Redis官方提供的Jedis。和Apache提供的common-pool2。

因为Jedis对象不是线程安全的。所以使用common-pool，对象池来管理Jedis对象。

 

分别对应两个jar包。

maven坐标为：

```xml
    <dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>3.4.1</version>
    </dependency>
    <dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-pool2</artifactId>
      <version>2.9.0</version>
    </dependency>
```



## 简单使用

Jedis的常用的方法名和参数名，基本都和Redis的命令和参数一样。

只演示一下Jedis对象创建、Jedis连接池和事务

### 创建Jedis对象

Jedis类有多个重载构造器

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102181753031.png" alt="image-20210102181753031" style="zoom: 50%;" />



常用的是`Jedis(String host int port)`，参数分别为主机号和端口号。





### Jedis连接池

创建一个工具类JedisPool，在类中有两个静态方法open、close和一个静态变量JedisPool。

open用来从连接池中获取一个Jedis对象，close负责关闭该链接。

```java
public class RedisUtils {

    public static JedisPool jedisPool = null;

    public static JedisPool open(String host, int port) {
        if(jedisPool == null) {
            //创建Jedis连接池配置对象
            JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
            //设置最大线程数，一个线程就是一个Jedis实例。默认为8
            jedisPoolConfig.setMaxTotal(10);
            //设置最大空闲实例数，设置这个可以保留足够连接，快速获取到Jedis对象
            jedisPoolConfig.setMaxIdle(2);
			//设置检查项为true，表示从线程池中获取到的Jedis对象一定是可用的
            jedisPoolConfig.setTestOnBorrow(true);
            //创建Jedis连接池。参数分别为配置，主机地址和端口。(还可以加上超时连接时间，访问密码)
            jedisPool = new JedisPool(jedisPoolConfig, host, port);

        }
        return jedisPool;
    }
    
    public static void close() {
        if(jedisPool != null) {
            jedisPool.close();
        }
    }

}
```

然后使Jedis的`PoolgetResource()`方法来获取Jedis对象，并用try/catch语句块。在finally中关闭jedis对象。等于是将对象放回实例池中，并不是销毁该对象。

### 事务

Jedis中有事务Transcation类。

使用`Jedis.multi()`返回一个事务对象，通过该对象添加操作命令。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102182350395.png" alt="image-20210102182350395" style="zoom:67%;" />





