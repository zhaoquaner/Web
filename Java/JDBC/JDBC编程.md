# JDBC常用接口和类及其编程步骤

## 常用接口和类及其简介

1. DriverManager：用于管理JDBC驱动的服务类，该类主要功能是获取Connection对象，从而连接数据库。

    常用方法：

    | 函数原型                                                     | 功能                 | 参数说明                                             |
    | ------------------------------------------------------------ | -------------------- | ---------------------------------------------------- |
    | public  static Connection getConnection(String url,String user, String pass) | 与指定数据库进行连接 | url:数据库端口<br />user：用户名<br />pass：用户密码 |

    

2. Connection：代表数据库连接对象，每个Connection对象代表一个物理连接会话，要想访问数据库，必须先获得数据库连接。

    常用方法：

    | 函数原型                                       | 功能                                  | 参数说明     |
    | ---------------------------------------------- | ------------------------------------- | ------------ |
    | Statement createStatement()                    | 返回一个新创建的Statement对象         | 无           |
    | PreparedStatement prepareStatement(String sql) | 返回一个新创建的PreparedStatement对象 | sql：SQL语句 |
    | CallableStatement prepareCall(String sql)      | 返回一个新创建的CallableStatement对象 | sql：SQL语句 |

    控制事务的方法：

    | 函数原型                               | 功能                       | 参数说明                                                     |
    | -------------------------------------- | -------------------------- | ------------------------------------------------------------ |
    | Savepoint setSavepoint()               | 创建一个保存点             | 无                                                           |
    | Savepoint setSavepoint(String name)    | 以指定的名字创建一个保存点 | name：指定保存点的名字                                       |
    | void rollback(Savepoint savepoint)     | 将事务会滚到指定保存点     | savepoint:指定保存点                                         |
    | void rollback()                        | 回滚事务                   | 无                                                           |
    | void setAutoCommit(boolean autoCommit) | 设置自动提交，即设置事务   | autoCommit:当为false时，关闭自动提交，打开事务<br />当为true，打开自动提交，关闭事务。<br /> |
    | void commit()                          | 提交事务                   | 无                                                           |

    

3. Statement：用于执行SQL语句的工具接口，该对象既可用于执行DDL、DCL语句，也可用于执行DML语句，还可用于SQL查询，当执行SQL查询时，返回查询得到的结果集。

    常用方法：

    | 函数原型                           | 功能                                                         | 参数说明         |
    | ---------------------------------- | ------------------------------------------------------------ | ---------------- |
    | ResultSet executeQuery(String sql) | 执行查询语句，并返回查询结果                                 | sql：查询语句    |
    | int executeUpdate(String sql)      | 执行DML，返回受影响行数,也可用于执行DDL语句,返回0            | sql:DML或DDL语句 |
    | boolean execute(String sql)        | 执行任何SQL语句，如果执行后第一个结果为ResultSet，返回true<br />如果为受影响行数或没有结果，返回0 | sql：SQL语句     |

4. PreparedStatement:预编译的对象。PreparedStatement是Statement的子接口，它允许数据库预编译SQL语句(这些SQL语句通常带有参数)，以后每次只改变SQL命令的参数，不用重新编译，性能更好。

    常用方法：

    `void setXxx(int parameterIndex, Xxx value)`

     该方法根据传入参数值的类型不同，需要使用不同的方法，传入的值根据索引传给SQ语句指定位置的参数。

    #### 注意

    PreparedStatement同样有上述Statement的方法，只不过没有参数sql，因为PreparedStatement已经预编译了SQL命令。

    

5. ResultSet：结果集对象，该对象包含访问查询结果的方法。

    常用方法：

    | 函数原型                  | 功能                                                         | 参数说明              |
    | ------------------------- | ------------------------------------------------------------ | --------------------- |
    | void close()              | 释放结果集对象                                               | 无                    |
    | boolean absolute(int row) | 将结果集的记录指针移动到第row行<br />如果row为负数，则移到倒数第row行 | row：指定对应行的位置 |
    | void beforeFirst()        | 将记录指针移动到首行之前，如果所指记录<br />是有效记录，返回true，否则返回false | 无                    |
    | boolean first()           | 将记录指针移动到第一行，返回值同上                           | 无                    |
    | boolean previous()        | 将记录指针移动到上一行，返回值同上                           | 无                    |
    | boolean next()            | 移动至下一行，返回值同上                                     | 无                    |
    | boolean last()            | 移动至最后一行，返回值同上                                   | 无                    |
    | void afterLast()          | 记录指针移动至最后一行之后                                   | 无                    |
    |                           |                                                              |                       |

    #### 注意

    结果集行从1开始。



## JDBC编程步骤

1. 加载数据库驱动：通常使用Class类的forName()静态方法加载驱动。

    `Class.forName(driverClass)`

    driverClass就是数据库驱动类对应字符串，不同数据库系统是不同的。

    加载MySQL驱动：`Class.forName("com.mysql.cj.jdbc.Driver")`

    

2. 通过DriverManager获取数据库连接

    `DriverManager.getConnection(String url, String user, String pass)`

    参数分别为：数据库URL, 登陆数据库用户名和密码

    URL遵循如下写法：

    jdbc:subprotocol:other stuff

    MySQL数据库URL写法为：jdbc:mysql://hostname:port/databasename

    其中，hostname是数据库主机名称，在本机上就是localhost

    port就是端口，是3306，databasename就是要使用的数据库。

    

3. 通过Connection对象创建Statement对象，创建方法有三个：

    | 函数原型                     | 功能                                       | 参数说明     |
    | ---------------------------- | ------------------------------------------ | ------------ |
    | createStatement()            | 创建基本的Statement对象                    | 无           |
    | prepareStatement(String sql) | 根据传入的SQL语句创建预编译的Statement对象 | sql：SQL语句 |
    | prepareCall(String sql)      | 根据传入的SQL语句创建CallableStatement对象 | sql：SQL语句 |

    
    

4. 使用Statement对象执行SQL语句，所有Statement对象都有三个方法执行SQL语句

    

    | 函数原型                 | 功能                             | 参数说明 |
    | ------------------------ | -------------------------------- | -------- |
    | execute()                | 可以执行任何SQL语句，但比较麻烦  | 无       |
    | executeUpdate()          | 主要执行DML和DDL语句             | 无       |
    | ResultSet executeQuery() | 只能执行查询语句，返回查询结果集 | 无       |

    
    

5. 操作结果集。如果是查询语句，可以操作ResultSet对象取出查询结果，并进行操作。

    除了上述ResultSet常用操作方法外，还有一个：

    `getXxx()` 该方法获取记录指针指向的行，特定列的值，可以传入列索引为参数，也可以传入列名为参数。

    **列索引从1开始**。

    

6. 回收数据库资源：包括关闭ResultSet资源、Statement和Connection资源。

    1. 推荐将这些对象的定义放在try块，程序会自动回收。

    2. 也可以使用对象的close()方法。











