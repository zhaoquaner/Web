# 7-动态SQL

## 概念

​		动态SQL，是指sql的内容是变化的，可以根据不同的条件来获取不同sql语句，主要是where部分发生变化。

使用MyBatis提供的各种标签堆条件做出判断来实现动态拼接Sql语句。常见的动态SQL标签有<if>、<where>、<choose>、<foreach>等。

​		主要解决的是查询条件不确定的情况，在程序运行期间，根据用户提交的查询条件进行查询。提交的查询条件不同，那么执行的SQL语句也不同，如果将每种情况都列出，会产生大量SQL语句，因此，可使用动态SQL解决这样的问题。





## 实体符号

在映射文件中的动态SQL中若出现，大于号(>)，小于号(<)，大于等于号(>=)，小于等于号(<=)等符号，应该转换为实体符号，否则XML文件可能解析错误。尤其是小于号(<)。

实体符号表

| 符号 | 含义     | 实体符号             |
| ---- | -------- | -------------------- |
| `<`  | 小于     | `&lt;`(less than)    |
| `>`  | 大于     | `&gt;`(greater than) |
| `>=` | 大于等于 | `&gt;=`              |
| `<=` | 小于等于 | `&lt;=`              |



## if标签

if标签的语法格式为：

```xml
<if test="使用参数Java对象的属性值作为判断条件，语法：属性=xxx值">
	sql语句
</if>
```

当if标签中条件成立时，才会拼接if中的sql语句。

简单例子：
DAO接口的方法：

```java
    List<Student> selectStudentIf(Student student);
```

映射文件：

```xml
    <select id="selectStudentIf" resultType="org.example.domain.Student" >
        select * from student
        where
        <!--只有当Java对象传来的参数name不为空，且不为空字符串时，才会加入该语句-->
        <if test="name != null and name != '' " >
            name = #{name}
        </if>
        <！--当age大于零时，拼接下面的sql语句-->
        <if test="age > 0" >
            and age &gt; #{age}
        </if>

    </select>
```

测试代码：

```java
    @Test
    public void testSelectStudentIf() {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        Student student = new Student();
        student.setName("赵四");
        student.setAge(20);
        List<Student> list = dao.selectStudentIf(student);
        for(Student stu : list) {
            System.out.println(stu);
        }
        
    }
```

可以运行成功。

但是有一个问题，当name为空，而年龄不为空时，where语句变成 `where and age > #{age}`，这个语句语法是错误的。会报错。

所以为了避免这种情况，需要在where语句中加入一个不影响查询结果和后面if语句的恒为真条件。例如`1 = 1`

那么映射文件中select标签需要改写为：

```xml
    <select id="selectStudentIf" resultType="org.example.domain.Student" >
        select * from student
        where 1 = 1
        <if test="name != null and name != '' " >
            and name = #{name}
        </if>
        <if test="age > 0" >
            and age &gt; #{age}
        </if>

    </select>
```

这样，无论在什么条件，sql语句都能够正常执行。**并且注意，加上恒成立条件`1 = 1`后，第一个if标签中要加上and**



## where标签

if标签比较麻烦的地方就是需要在where后面手动加上`1= 1`的子句。

而where标签可以实现，在有查询条件时，自动加上where子句；没有查询条件时，不会添加where子句。

语法格式：

```xml
        <where>
            <if test="" >
                and ...
            </if>
            <if test="" >
                and ...
            </if>
            ..
        </where>
```

其实就是where标签中嵌套if标签。

需要注意的是：第一个if标签中，可以没有and，也可以写上and，系统会自动把多余and去掉。





## foreach标签

<foreach>标签用于实现对数组和集合的遍历。常用在in语句中，in的语法格式为`where 列名 in (成员1，成员2，成员3,...)`

语法格式为：

```xml
        <foreach collection="" item="" open="" close="" separator="" >
            #{item的值}
        </foreach>
```

foreach标签有五个属性：

- collection：表示要遍历的集合类型，如果是数组，值就是array；如果是List集合，值就是list
- item：自定义的变量，代表要遍历的集合变量，相当于`for(Student stu:list)`中的stu
- open：开始的字符，一般是`(`
- close：结束的字符，一般是`)`
- separator：集合成员之间的分隔符，一般是`,`





有两种常用方式，一种是遍历简单类型，一种是遍历引用类型。

区别就是占位符#{item的值}有所不同。**简单类型`#{}`里面直接是item定义的值，而引用类型里需要再使用`.`符号使用引用类型的成员变量。如`#{student.id}。`**



使用两种方式的例子：

### 遍历简单类型

简单类型就是Java的基本数据类型加上String。

例子：
DAO接口方法：

```java
    List<Student> selectStudentsByIds(List<Integer> list);
```

映射文件：

```xml
    <select id="selectStudentsByIds" resultType="org.example.domain.Student">
        select * from student where id in
        <foreach collection="list" item="id" open="(" close=")" separator="," >
            #{id}
        </foreach>
    </select>
```

测试代码：

```java
    @Test
    public void testSelectStudentsByIds() {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        List<Integer> list = new ArrayList<>();
        list.add(1001);
        list.add(1002);
        list.add(1003);
        List<Student> students = dao.selectStudentsByIds(list);
        for(Student stu:students) {
            System.out.println(stu);
        }

    }
```

运行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201204104546520.png" alt="image-20201204104546520" style="zoom:50%;" />



### 遍历引用类型

DAO接口的方法：

```java
    List<Student> selectStudentsByIds(List<Student> list);
```

映射文件：

```xml
    <select id="selectStudentsByIds" resultType="org.example.domain.Student">
        select * from student where id in
        <foreach collection="list" item="student" open="(" close=")" separator="," >
            #{student.id}
        </foreach>
    </select>
```

测试代码：

```java
    @Test
    public void testSelectStudentsByIds() {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao dao = sqlSession.getMapper(StudentDao.class);
        List<Student> list = new ArrayList<>();
        Student student1 = new Student();
        student1.setId(1001);
        list.add(student1);
        Student student2 = new Student();
        student2.setId(1002);
        list.add(student2);
        List<Student> students = dao.selectStudentsByIds(list);
        for(Student stu:students) {
            System.out.println(stu);
        }

    }
```

运行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201204104945216.png" alt="image-20201204104945216" style="zoom:50%;" />







## sql标签

<sql>标签用于定义SQL片段，让其他SQL标签复用。

语法格式：

```xml
<sql id="" >
	sql语句
</sql>
```

属性id用来标识该sql语句，是一个唯一值。



使用<include>标签来使用该语句。语法格式为：

```xml
<include refid="" />
```

属性refid就是需要使用的sql语句的id



例子：

```xml
    <sql id="select" >
        select * from student
    </sql>
    
    <select id="selectStudentsByIds" resultType="org.example.domain.Student">
        <include refid="select" />
        where id in
        <foreach collection="list" item="student" open="(" close=")" separator="," >
            #{student.id}
        </foreach>
    </select>
```











