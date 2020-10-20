# 执行SQL语句

## Statement对象执行SQL语句



1. 使用executeLargeUpdate()方法执行DDL和DML语句

    executeUpdate和executeLargeUpdate区别是如果受影响的行数超过了int最大值，则要使用后者。

    例子：

    

    ```java
    import java.util.*;
    import java.io.*;
    import java.sql.*;
    
    public class ExecuteDDL
    {
    	private String driver;
    	private String url;
    	private String user;
    	private String pass;
    	public void initParam(String paramFile)
    		throws Exception
    	{
    		// 使用Properties类来加载属性文件
    		var props = new Properties();
    		props.load(new FileInputStream(paramFile));
    		driver = props.getProperty("driver");
    		url = props.getProperty("url");
    		user = props.getProperty("user");
    		pass = props.getProperty("pass");
    	}
    	public void createTable(String sql) throws Exception
    	{
    		// 加载驱动
    		Class.forName(driver);
    		try (
    			// 获取数据库连接
    			Connection conn = DriverManager.getConnection(url, user, pass);
    			// 使用Connection来创建一个Statment对象
    			Statement stmt = conn.createStatement())
    		{
    			// 执行DDL,创建数据表
    			stmt.executeUpdate(sql);
    		}
    	}
    	public static void main(String[] args) throws Exception
    	{
    		var ed = new ExecuteDDL();
    		ed.initParam("mysql.ini");
    		ed.createTable("create table jdbc_test "
    			+ "( jdbc_id int auto_increment primary key, "
    			+ "jdbc_name varchar(255), "
    			+ "jdbc_desc text);");
    		System.out.println("-----建表成功-----");
    	}
    }
    ```

    
    
2. 使用execute方法执行SQL语句

    execute方法可以执行任何SQL语句，但是很麻烦，通常没必要用此方法。但如果不清楚SQL语句类型，则可以使用此方法。

    该方法返回值是boolean值，SQL语句返回结果集，则为true，否则为false。

    在该方法执行以后，若是true，则返回值为结果集

    可用`getResultSet()` 获取Statement执行查询语句的结果集

    若返回值是false，则表明SQL语句是DDL或者DML，用

    `getUpdateCount()` 获取执行DML语句影响的行数


    例子：
    
    ```java
    
    import java.util.*;
    import java.io.*;
    import java.sql.*;
    
    public class ExecuteSQL
    {
    	private String driver;
    	private String url;
    	private String user;
    	private String pass;
    	public void initParam(String paramFile) throws Exception
    	{
    		// 使用Properties类来加载属性文件
    		var props = new Properties();
    		props.load(new FileInputStream(paramFile));
    		driver = props.getProperty("driver");
    		url = props.getProperty("url");
    		user = props.getProperty("user");
    		pass = props.getProperty("pass");
    	}
    	public void executeSql(String sql) throws Exception
    	{
    		// 加载驱动
    		Class.forName(driver);
    		try (
    			// 获取数据库连接
    			Connection conn = DriverManager.getConnection(url,
    				user, pass);
    			// 使用Connection来创建一个Statement对象
    			Statement stmt = conn.createStatement())
    		{
    			// 执行SQL,返回boolean值表示是否包含ResultSet
    			boolean hasResultSet = stmt.execute(sql);
    			// 如果执行后有ResultSet结果集
    			if (hasResultSet)
    			{
    				try (
    					// 获取结果集
    					ResultSet rs = stmt.getResultSet())
    				{
    					// ResultSetMetaData是用于分析结果集的元数据接口
    					ResultSetMetaData rsmd = rs.getMetaData();
    					int columnCount = rsmd.getColumnCount();
    					// 迭代输出ResultSet对象
    					while (rs.next())
    					{
    						// 依次输出每列的值
    						for (var i = 0; i < columnCount; i++)
    						{
    							System.out.print(rs.getString(i + 1) + "\t");
    						}
    						System.out.print("\n");
    					}
    				}
    			}
    			else
    			{
    				System.out.println("该SQL语句影响的记录有"
    					+ stmt.getUpdateCount() + "条");
    			}
    		}
    	}
    	public static void main(String[] args) throws Exception
    	{
    		var es = new ExecuteSQL();
    		es.initParam("mysql.ini");
    		System.out.println("------执行删除表的DDL语句-----");
    		es.executeSql("drop table if exists my_test");
    		System.out.println("------执行建表的DDL语句-----");
    		es.executeSql("create table my_test"
    			+ "(test_id int auto_increment primary key, "
    			+ "test_name varchar(255))");
    		System.out.println("------执行插入数据的DML语句-----");
    		es.executeSql("insert into my_test(test_name) "
    			+ "select student_name from student_table");
    		System.out.println("------执行查询数据的查询语句-----");
    		es.executeSql("select * from my_test");
    	}
    }
    
    ```


​    

​    


    ## PreparedStatement对象执行SQL语句
    
    如果要反复执行结构相同的SQL语句，可以用带占位符 ？参数的SQL语句来代替语句的不同地方
    
    `insert into student_table values(null, ?, ?)`
    
    可以用PreparedStatement来创建这样语句的对象，在执行语句之前，必须要为占位符传入对应参数值，用
    
    `setXxx(int index, Xxx value)方法来传入参数值。
    
    **如果清楚占位符参数对应数据类型，可以使用对应setXxx()传入参数值，若不知道对应数据类型，则用setObject()传入，PreparedStatement负责类型转换**。
    
    例子：
    
    ```java
    
    import java.util.*;
    import java.io.*;
    import java.sql.*;
    
    public class PreparedStatementTest
    {
    	private String driver;
    	private String url;
    	private String user;
    	private String pass;
    	public void initParam(String paramFile) throws Exception
    	{
    		// 使用Properties类来加载属性文件
    		var props = new Properties();
    		props.load(new FileInputStream(paramFile));
    		driver = props.getProperty("driver");
    		url = props.getProperty("url");
    		user = props.getProperty("user");
    		pass = props.getProperty("pass");
    		// 加载驱动
    		Class.forName(driver);
    	}
    	public void insertUseStatement() throws Exception
    	{
    		long start = System.currentTimeMillis();
    		try (
    			// 获取数据库连接
    			Connection conn = DriverManager.getConnection(url,
    				user, pass);
    			// 使用Connection来创建一个Statment对象
    			Statement stmt = conn.createStatement())
    		{
    			// 需要使用100条SQL语句来插入100条记录
    			for (var i = 0; i < 100; i++)
    			{
    				stmt.executeUpdate("insert into student_table values("
    					+ " null, '姓名" + i + "', 1)");
    			}
    			System.out.println("使用Statement费时:"
    				+ (System.currentTimeMillis() - start));
    		}
    	}
    	public void insertUsePrepare() throws Exception
    	{
    		long start = System.currentTimeMillis();
    		try (
    			// 获取数据库连接
    			Connection conn = DriverManager.getConnection(url,
    				user, pass);
    			// 使用Connection来创建一个PreparedStatement对象
    			PreparedStatement pstmt = conn.prepareStatement(
    				"insert into student_table values(null, ?, 1)"))
    
    		{
    			// 100次为PreparedStatement的参数设值，就可以插入100条记录
    			for (var i = 0; i < 100; i++)
    			{
    				pstmt.setString(1, "姓名" + i);
    				pstmt.executeUpdate();
    			}
    			System.out.println("使用PreparedStatement费时:"
    				+ (System.currentTimeMillis() - start));
    		}
    	}
    	public static void main(String[] args) throws Exception
    	{
    		var pt = new PreparedStatementTest();
    		pt.initParam("mysql.ini");
    		pt.insertUseStatement();
    		pt.insertUsePrepare();
    	}
    }
    
    ```



使用PreparedStatement比使用Statement多了三个好处

1. PreparedStatement预编译SQL语句，性能更好

2. PreparedStatement不需要拼接SQL语句，编程更简单

3. PreparedStatement可以防止SQL注入，安全性更好。


​        


​        

## 使用CallableStatement调用存储过程

通过Connection的prepareCall()来创建CallableStatement对象，创建时要传入调用存储对象的SQL语句，语句格式为
{call 过程名(?, ?, ?...)},问号作为存储过程参数的占位符，如下代码创建了调用存储过程的CallableStatement对象

`cstmt = conn.prepareCall("{call add_pro(?, ?, ?)}")`

传入参数可以通过 `setXxx()` 来传入参数
传出参数需要调用 `registerOutParameter()` 来注册该参数，实例代码如下

``````java
//注册CallableStatement的第三个参数是int类型
cstmt.registerOutParameter(3, Types.INTEGER)
``````

调用存储过程实例：

```java
import java.util.*;
import java.io.*;
import java.sql.*;

public class CallableStatementTest
{
	private String driver;
	private String url;
	private String user;
	private String pass;
	public void initParam(String paramFile) throws Exception
	{
		// 使用Properties类来加载属性文件
		var props = new Properties();
		props.load(new FileInputStream(paramFile));
		driver = props.getProperty("driver");
		url = props.getProperty("url");
		user = props.getProperty("user");
		pass = props.getProperty("pass");
	}
	public void callProcedure() throws Exception
	{
		// 加载驱动
		Class.forName(driver);
		try (
			// 获取数据库连接
			Connection conn = DriverManager.getConnection(url, user, pass);
			// 使用Connection来创建一个CallableStatment对象
			CallableStatement cstmt = conn.prepareCall(
				"{call add_pro(?, ?, ?)}"))
		{
			cstmt.setInt(1, 4);
			cstmt.setInt(2, 5);
			// 注册CallableStatement的第三个参数是int类型
			cstmt.registerOutParameter(3, Types.INTEGER);
			// 执行存储过程
			cstmt.execute();
			// 获取，并输出存储过程传出参数的值。
			System.out.println("执行结果是: " + cstmt.getInt(3));
		}
	}
	public static void main(String[] args) throws Exception
	{
		var ct = new CallableStatementTest();
		ct.initParam("mysql.ini");
		ct.callProcedure();
	}
}


```





















































 

