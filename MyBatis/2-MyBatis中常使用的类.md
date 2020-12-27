# 2-MyBatis中常用的类

## Resources

负责读取主配置文件的，`InputStream in = Resources.getResourceAsStram("mybatis.xml");`

没有其他作用

## SqlSessionFactoryBuilder

负责创建SqlSessionFactory对象

```java
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
SqlSessionFactory factory = builder.build(in);
```



## SqlSessionFactory

它是一个接口，用来获取SqlSession对象。是比较重量级的对象，程序创建该对象耗时较长，使用的资源多，在整个项目中，有一个就够用了。

使用该接口的openSession()方法来获取SqlSession对象，该方法有几种重载形式，常见有

- openSession()：获取一个非自动提交事务的SqlSession对象
- openSession(boolean autoCommit)：参数是boolean，值为true，表示获取自动提交事务的SqlSession；false，表示非自动提交事务的SqlSession对象。



## SqlSession

是一个接口，定义了操作数据的方法，常见有查询，插入，更新，删除操作。

但注意：SqlSession对象不是线程安全的，需要在方法内部使用，在执行sql语句之前，使用openSession()方法获取SqlSession对象，执行完sql语句后，执行SqlSession.close()关闭它。



## 工具类

可以把加载主配置文件，创建SqlSessionFactory等操作封装起来，创建一个工具类。

```java
public class MyBatisUtils {
    
    private static SqlSessionFactory sqlSessionFactory = null;

    static {
        try {
            String config = "mybatis.xml";
            InputStream in = Resources.getResourceAsStream(config);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(in);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getSqlSession() {
        SqlSession sqlSession = null;
        if(sqlSessionFactory != null) {
            sqlSession = sqlSessionFactory.openSession();
        }

        return sqlSession;
    }

}
```



