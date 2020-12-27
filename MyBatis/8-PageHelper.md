# 8-PageHelper

PageHelper插件是一个通用分页插件，它是用来给查询得到的数据分页的。

使用pagehelper的步骤：

## 加入maven依赖

坐标：

```xml
<dependency>
 <groupId>com.github.pagehelper</groupId>
 <artifactId>pagehelper</artifactId>
 <version>5.2.0</version>
</dependency>

```

## 加入plugin配置(配置拦截器)

在mybatis配置文件中

在<environments>标签之前加入  

```xml
<plugins>
 <plugin interceptor="com.github.pagehelper.PageInterceptor" />
</plugins>
```

## 调用PageHelper静态方法

在需要进行分页的MyBatis查询方法之前调用`PageHelper.startPage()`静态方法即可，紧跟在这个方法后的第一个MyBatis查询方法会被分页。



startPage方法有几个重载形式：

- `startPage(int pageNum, int pageSize)`
- `startPage(int pageNum, int pageSize, boolean count)`
- `startPage(int pageNum, int pageSize, String orderBy)`
- `startPage(int pageNum, int pageSize, boolean count, Boolean reasonable, Boolean pageSizeZero)`

参数含义：

- pageNum：页码，就是要需要显示第几页
- pageSize：每页显示数量
- count：是否进行count查询
- reasonable：分页合理化，null时使用默认配置
- pageSizeZero：当为true且pageSize=0时返回全部结果；当为false时分页。null时使用默认配置

例子：
要查询数据库中所有的数据

DAO接口：

```java
    List<Student> selectStudents();
```

映射文件：

```xml
    <select id="selectStudents" resultType="org.example.domain.Student">
        select * from student
    </select>
```

测试代码：

```java
    @Test
    public void testSelectStudents() {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        PageHelper.startPage(1, 2);
        List<Student> list = dao.selectStudents();
        for(Student stu:list) {
            System.out.println(stu);
        }

    }
```

数据表：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201204120512650.png" alt="image-20201204120512650" style="zoom:50%;" />



运行结果：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201204120544522.png" alt="image-20201204120544522" style="zoom:50%;" />



**可以看到，在执行查询前，加上了count操作，用来查询该表有多少行，在查询语句后加上了`Limit ?`。**









除此之外PageHelper还有其他几个方法

### offsetPage

有两种重载形式

- `offsetPage(int offset, int limit)`
- `offsetPage(int offset, int limit, boolean count)`

参数含义：

- offset：起始位置，也就是从哪行开始数。同时**包括该行。注意：应该按第一行为0的数法。也就是说，如果从第一行开始显示，这个参数应该设为0**
- limit：每页显示的数量
- count：是否进行count查询

例子：

接口方法和映射文件不变。

测试代码改为`PageHelper.offsetPage(0, 2)`

运行结果：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201204120929472.png" alt="image-20201204120929472" style="zoom:50%;" />





### orderBy

方法`orderBy(String orderBy)`，可以设置查询结果按什么排序，参数orderBy是列名



### count

方法`long count(ISelect select)`，可以返回任意查询方法的行数。

ISelect是一个接口，只有一个方法`void doSelect()`，可以使用匿名内部类实现它，在该方法中调用自己的查询方法。

例如：

```java
    @Test
    public void testSelectStudents() {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        long count = PageHelper.count(new ISelect() {
            @Override
            public void doSelect() {
                dao.selectStudents();
            }
        });
        System.out.println(count);

    }
```





