# 3-传统DAO和动态代理

​		在之前使用MyBatis的时候，我们首先创建了实体类，接口，映射文件和主配置文件，然后在测试代码中加载主配置文件，然后调用SqlSession的方法来操作数据库，参数是sqlId，**但是，我们可以发现，接口在整个过程中并没有发挥作用，我们只是将mapper的namespace设为接口的全限定名称，id是接口内的方法名，但这两个其实是自定义的，它并不和接口挂钩。**

​		那么为什么还要创建接口呢？



首先回顾一下传统DAO应该如何使用

## 传统DAO

​		这部分比较简单，不做例子分析。

​		在传统DAO中，首先创建出接口，映射文件和主配置文件，然后创建接口的实现类，实现接口的方法，其实就相当于把测试方法中的代码挪到实现类中的方法。然后创建实现类对象，调用方法来访问数据库。

​		也就是我们实际上是需要手动创建出接口的实现类的。

## 动态代理

​		在映射文件中，规定了mapper的namespace必须是接口的全限定类名，id必须是接口的方法名。这么做是必要的。因为dao对象，类型是DAO接口，全限定类名和namespace一致，实现类的方法名称也和id值一致。

​		同时，dao中方法的返回值也可以确定SqlSession要调用的方法。

​			如果返回值是List，那么就调用selectList方法；如果是int，那么看mapper文件中insert、update标签，就会调用对应方法。

​		那么就可以使用MyBatis的动态代理，来让MyBatis框架创建出接口的实现类，而不需要手动创建，底层原理实际就是使用的反射。

MyBatis的动态代理：

​		mybatis根据dao的方法调用，获取执行sql语句的信息，mybatis根据dao接口，创建出一个dao接口的实现类，并创建这个类的对象，完成sqlSession调用方法，访问数据库。



​		**我们使用SqlSession的getMapper(Class)方法来获取接口的实现类对象，参数是class。**



例子：

改写之前的查询操作

映射文件和接口不需要修改，只需要在使用时，获取SqlSession对象后，调用它的getMapper方法即可。

```java
    public void testSelectStudent() throws IOException {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        List<Student> list = dao.selectStudents();
        for(Student stu : list) {
            System.out.println(stu);
        }
    }
```

从代码中可以看出，getMapper返回接口实现类，然后用该实现类去调用对应方法，那么MyBatis就会根据该类的全限定类名和调用的方法名去映射文件中找到对应sql语句，并执行它，返回结果。





实际开发中，这种用法是很常见的。