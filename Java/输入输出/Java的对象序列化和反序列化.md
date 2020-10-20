# 才会Java的对象序列化和反序列化

## 序列化的含义和意义



对象序列化的目标就是将对象保存到磁盘中，或者直接在网络传输对象，对象序列化机制允许把内存中的Java对象转换成平台无关的二进制流，从而允许永久保存在磁盘上。程序一旦获得了这种二进制流，都可以将这种二进制流恢复成原来的Java对象,即反序列化。

对象的序列化(Serialize)指将一个Java对象写入IO流，与此对应，对象的反序列化，指从IO流中恢复该Java对象。

Java 9增强了对象序列化机制，它允许对读入的序列化数据进行过滤，这种过滤在反序列化之前对数据进行校验，从而提高了安全性和健壮性。



如果需要让某个对象支持序列化机制，则必须让它的类是可序列化的，该类必须实现两个接口之一：

- Serializable
- Externalizable

Java很多类实现了Serializable，该接口为标记接口，无需实现任何方法，只是表明该类的实例是可以序列化的。



所有在网络上传输的对象的类都应该是实例化的。



## 使用对象流实现序列化和反序列化

实现序列化应该实现Serializable接口或者Externalizable接口之一，这两接口区别和联系，后面会有介绍，暂时不用理会Externalizable接口。

使用Serializable接口实现序列化很简单，只要让对应的类实现该接口即可。然后程序可以通过两个步骤序列化对象

1. 创建一个ObjectOutputStream，这个输出流是一个处理流，必须建立在其他节点流的基础上

    ```java
    ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("object.txt"));
    ```

2. 调用ObjectOutputStream对象的writeObject()方法输出该可序列化对象

    ```java
    oos.writeObject(per);
    ```

    下面程序定义了一个可序列化的类：

    ```java
    class Person implements Serializable{
    
            private String name;
    
            private transient int age;
    
            public Person(String name, int age){
    
                    System.out.println("有参数的构造器");
    
                    this.name = name;
    
                    this.age = age;
            }
    
            public String getName() {
                    return name;
            }
    
            public void setName(String name) {
                    this.name = name;
            }
    
            public int getAge() {
                    return age;
            }
    
            public void setAge(int age) {
                    this.age = age;
            }
    
    ```

    然后使用ObjectOutputStream将Person对象写入磁盘文件

    ```java
    public  class  Main  {
    
            public  static  void  main(String[]  args) throws Exception{
    
                    try (ObjectOutputStream oos = new ObjectOutputStream(new 									FileOutputStream("transient.txt")))
                    {
                            Person per = new Person("孙悟空", 500);
    
                            oos.writeObject(per);
                    }
                    catch (Exception e)
                    {
                            e.printStackTrace();
                    }
    
    
    
            }
    }
    ```



从IO流中恢复该Java对象，即反序列化，也需两步：

1. 创建ObjectInputStream处理流：

    ```java
    ObjectInputStream ois = new ObjectInputStream(new FileInputStream("object.txt"));
    ```

2. 调用ObjectInputStream对象的readObject()方法读取流中的对象，该方法返回一个Object对象，如果程序知道该Java对象类型，应强制转换，代码：

    ```java
    Person p = (Person) ois.readObject();
    ```

    完整的从object文件读取Java对象：

    ```java
    public class ReadObject{
        public static void main(String args[])
        {
            try(ObjectInputStream ois = new ObjectInputStream(new FileInputStream(object.txt)))
            {
               Person p = (Person) ois.readObject();
                
                System.out.println("名字为" + p.getName() + "年龄为" + p.getAge());
            }
            catch(Exception e)
            {
                e.printStackTrace();
            }
        }
    }
    ```

    **注意**

    1. 反序列化读取的仅仅是Java对象的数据，不是Java类，因此采用反序列化恢复Java对象时，必须提供Java对象所属类的class文件，否则将会引发ClassNotFoundException异常。
    2. Person类只有一个有参数的构造器，没有无参数的构造器，而且该构造器有打印语句，但反序列化java对象时，没有看到程序调用该构造器，说明反序列化机制不需要通过构造器来初始化Java对象。
    3. **当使用序列化机制向文件写入多个Java对象，使用反序列化机制时必须按写入的顺序来读取**。
    4. 当一个可序列化类有多个父类时(包括直接父类和间接父类)，这些父类要么有无参数的构造器，要么也是可序列化的，否则反序列化时会抛出InvalidClassException异常，如果父类是不可序列化的，只是带有无参数的构造器，则该父类定义的成员变量值不会序列化到二进制流中。

    

    ## 对象引用的序列化

    

    之前介绍的序列化对象对应的类Person，两个成员变量分别是String和int类型，如果某个类的成员变量不是基本类型或者String类型，而是另一种引用类型，那么这个引用类型必须是可序列化的，否则拥有该变量的类也是不可序列化的。**换句话说，只有一个类的所有成员变量对应的类都是可序列化的，这个类才是可序列化的，基本类型和String已经实现了可序列化。**

    比如Teacher类里有一个成员变量是Person类，只有Person是可序列化的，Teacher才是可序列化的。

    

    那么现在假设有一种特殊的情形，程序有两个Teacher对象，它们的student实例变量都引用同一个Person对象，而且该Person对象还有一个引用变量引用它，代码如下：

    ```java
    Person per = new Person("孙悟空", 500);
    Teacher t1 = new Teacher("唐僧", per);
    Teacher t2 = new Teacher("菩提祖师", per);
    ```

    上面代码创建了两个Teacher对象和一个Person对象，这里产生了一个问题，如果先序列化t1对象，则系统会将t1对象引用的Person对象一起序列化，如果程序再序列化t2对象，系统会序列化t2对象，如果再序列化per对象，系统会再次序列化该Person对象，整个过程似乎会序列化三个Person对象。

    

    那么当输入流反序列化这些对象时，将会得到三个Person对象，从而引起t1和t2对象引用的不是同一个Person对象，这违背了Java序列化机制的初衷。

    所以Java序列化机制采用特殊序列化算法如下：

    - 所有保存到磁盘中的对象都有一个序列化编号
    - 当程序试图序列化一个对象，程序先检查该对象是否已经被序列化过，只有该对象从未(在本次虚拟机中)被序列化过，系统才会将该对象转换成字节序列输出
    - 如果某个对象已经被序列化，程序将只输出一个序列化编号，而不是重新序列化该对象

    根据上述序列化算法，可以知道当第二次第三次序列化Person对象时，程序将只会输出一个序列化编号。

    

    **注意**

    这种序列化算法也会引起潜在问题，当多次序列化一个对象时，只有第一次会序列化该对象，那么当第一次序列化该对象时，如果修改了该对象成员变量的值，再次序列化，也只会输出一个序列化编号，即第一次序列化的对象，因此改变的成员变量值不会被输出。

    **所以在序列化Java对象时，一定要注意，只有第一次序列化才会将对象转换成字节序列，在后面程序中，即使该对象实例变量发生改变，再次调用writeObject方法时，改变的实例变量也不会被输出。**

    

    ## Java 9增加的过滤功能

    

    Java 9为ObjectInputStream增加了setObjectFilter(),getObjectFilter()两个方法，其中一个方法为对象输入流设置过滤器，当程序通过ObjectInputStream反序列化对象时，过滤器的checkInput()方法会被自动激发，用于检查序列化数据是否有效。

    

    使用checkInput()方法检查序列化数据有三种返回值：

    - Status.REJECTED:拒绝恢复
    - Status.ALLOWED:允许恢复
    - Status.UNDECIDED:未决定状态，程序将继续执行检查

    ObjectInputStream会根据ObjectInputFilter检查结果来决定是否执行反序列化，如果返回Status.REJECTED，反序列化将会被阻止，返回ALLOWED，反序列化会被执行。

    

    例子：该程序会在反序列化之前对数据执行检查：

    

    ```java
    import java.io.*;
    
    
    public class FilterTest
    {
    	public static void main(String[] args)
    	{
    		try (
    			// 创建一个ObjectInputStream输入流
    			var ois = new ObjectInputStream(new FileInputStream("object.txt")))
    		{
    			ois.setObjectInputFilter((info) -> {
    				System.out.println("===执行数据过滤===");
    				ObjectInputFilter serialFilter = ObjectInputFilter.Config.getSerialFilter();
    					if (serialFilter != null) {
    						// 首先使用ObjectInputFilter执行默认的检查
    						ObjectInputFilter.Status status = serialFilter.checkInput(info);
    						// 如果默认检查的结果不是Status.UNDECIDED
    						if (status != ObjectInputFilter.Status.UNDECIDED) {
    							// 直接返回检查结果
    							return status;
    						}
    					}
    					// 如果要恢复的对象不是1个
    					if (info.references() != 1)
    					{
    						// 不允许恢复对象
    						return ObjectInputFilter.Status.REJECTED;
    					}
    					if (info.serialClass() != null &&
    						// 如果恢复的不是Person类
    						info.serialClass() != Person.class)
    					{
    						// 不允许恢复对象
    						return ObjectInputFilter.Status.REJECTED;
    					}
    					return ObjectInputFilter.Status.UNDECIDED;
    				});
    			// 从输入流中读取一个Java对象，并将其强制类型转换为Person类
    			var p = (Person) ois.readObject();
    			System.out.println("名字为：" + p.getName()
    				+ "\n年龄为：" + p.getAge());
    		}
    		catch (Exception ex)
    		{
    			ex.printStackTrace();
    		
    	}
    }
    ```

    上述代码重写了checkInput方法，先使用默认的ObjectInputFilter执行检查，如果检查结果不是Status.UNDECIDED，程序直接返回检查结果，接下来通过FilterInfo检查序列化数据，如果序列化数据不唯一，拒绝执行，如果不是Person对象，拒绝执行，通过这种检查，程序可以保证反序列化出来的是唯一的Person对象。

    

    ## 自定义序列化

    

    在一些特殊的情景，如果一个类的某些实例变量是敏感信息，这时不希望程序将该实例变量进行实例化，或者某个实例变量类型是不可序列化的，因此不希望对该实例变量进行递归序列化。

    

    **递归序列化**：

    当对某个对象进行序列化时，系统会自动把该对象的所有实例变量依次进行序列化，如果某个实例变量引用到另一个对象，则该引用的对象也会被序列化，这样一层一层的递归的进行对象序列化，就是递归序列化。

    

    通过在实例变量前面使用transient关键词修饰，可以指定Java序列化时无需理会该实例变量。

    例子：

    ```java
    public class Person
    	implements java.io.Serializable
    {
    	private String name;
    	private transient int age;
    	// 注意此处没有提供无参数的构造器!
    	public Person(String name, int age)
    	{
    		System.out.println("有参数的构造器");
    		this.name = name;
    		this.age = age;
    	}
    	// 省略name与age的setter和getter方法
    
    	// name的setter和getter方法
    	public void setName(String name)
    	{
    		this.name = name;
    	}
    	public String getName()
    	{
    		return this.name;
    	}
    
    	// age的setter和getter方法
    	public void setAge(int age)
    	{
    		this.age = age;
    	}
    	public int getAge()
    	{
    		return this.age;
    	}
    }
    ```

    **注意**

    transient关键词只能用于修饰实例变量，不可修饰Java程序其他成分。

    

    下面先序列化一个Person对象，然后反序列化读取，再输出age变量

    ```java
    import java.io.*;
    
    public class TransientTest
    {
    	public static void main(String[] args)
    	{
    		try (
    			// 创建一个ObjectOutputStream输出流
    			var oos = new ObjectOutputStream(new FileOutputStream("transient.txt"));
    			// 创建一个ObjectInputStream输入流
    			var ois = new ObjectInputStream(new FileInputStream("transient.txt")))
    		{
    			var per = new Person("孙悟空", 500);
    			// 系统会per对象转换字节序列并输出
    			oos.writeObject(per);
    			var p = (Person) ois.readObject();
    			System.out.println(p.getAge());
    		}
    		catch (Exception ex)
    		{
    			ex.printStackTrace();
    		}
    	}
    }
    ```

    age使用transient关键词修饰，所以输出age的值为0.

    

    使用transient关键词修饰实例变量虽然简单，方便，但是所修饰的实例变量将被完成隔离在序列化机制之外，这样导致反序列化时无法取得该实例变量值，Java提供了一种自定义序列化机制，可以让程序控制如何序列化各实例变量。

    需要特殊处理的类应该提供如下特殊签名的方法，这些方法用于实现自定义序列化。

    

    **private void writeObject(java.io.ObjectOutputStream out) throws IOException**

    功能：负责写入特定类的实例状态，以便readObject()如何恢复它

    参数:out:特定对象输出流

    返回值：无

    **注：**

    默认情况，该方法会调用out.defaultWriteObject来保存Java对象实例变量。

    ---

    

    **private void writeObject(java.io.ObjectOutputStream out) throws IOException**

    功能：负责从流中读取并恢复实例变量

    参数:in:特定对象输入流

    返回值：无

    **注：**

    默认情况，该方法会调用in.defaultReadObject来恢复Java对象的非瞬态实例变量，通常情况，readObject和writeObject方法相对应。

    ---

    **private void readObjectNoData() throws ObjectStreamException**

    功能：如果序列化流不完整，此方法可以正确初始化反序列化的对象，例如，接收方使用的反序列化类版本不同于发送方，或者序列化流被篡改时，系统调用此方法初始化反序列化的对象。

    ---

    下面Person类重写了writeObject方法和readObject方法，其中写入时先将name变量包装成SringBuffer，然后反转进行写入，读取时，先将读取的数据强制类型转换为StringBuffer，再将其反转后赋给name变量。

    ```java
    import java.io.*;
    
    public class Person
    	implements java.io.Serializable
    {
    	private String name;
    	private int age;
    	// 注意此处没有提供无参数的构造器!
    	public Person(String name, int age)
    	{
    		System.out.println("有参数的构造器");
    		this.name = name;
    		this.age = age;
    	}
    	// 省略name与age的setter和getter方法
    
    	// name的setter和getter方法
    	public void setName(String name)
    	{
    		this.name = name;
    	}
    	public String getName()
    	{
    		return this.name;
    	}
    
    	// age的setter和getter方法
    	public void setAge(int age)
    	{
    		this.age = age;
    	}
    	public int getAge()
    	{
    		return this.age;
    	}
    
    	private void writeObject(java.io.ObjectOutputStream out)
    		throws IOException
    	{
    		// 将name实例变量的值反转后写入二进制流
    		out.writeObject(new StringBuffer(name).reverse());
    		out.writeInt(age);
    	}
    	private void readObject(java.io.ObjectInputStream in)
    		throws IOException, ClassNotFoundException
    	{
    		// 将读取的字符串反转后赋给name实例变量
    		this.name = ((StringBuffer) in.readObject()).reverse()
    			.toString();
    		this.age = in.readInt();
    	}
    }
    ```

    对于这个Person类而言，序列化和反序列化Person实例与不重写两个方法效果一样，区别在于序列化后的处理流，即使有Cracker截获到Person对象流，它看到的也是加密后的name，提高了序列化的安全性。

**注意：**

writeObject方法存储实例变量的顺序应该和readObject方法恢复实例变量的顺序一致。





还有一种更彻底的自定义机制，它甚至可以在序列化对象之前，将该对象替换成其他对象，如果需要，则应该为序列化类提供如下特殊方法：

```java
ANY_ACCESS_MODIFIER Object writeReplace() throws ObjectStreamException
```

此序列化方法由序列化机制调用，只要该方法存在。因为该方法可以拥有private、protected和包私有权限，所以子类可能获得该方法。

下面例子提供了writeReplace方法，可以在写入Person对象是将该对象替换成ArrayList

```java

import java.util.*;
import java.io.*;

public class Person
	implements java.io.Serializable
{
	private String name;
	private int age;
	// 注意此处没有提供无参数的构造器!
	public Person(String name, int age)
	{
		System.out.println("有参数的构造器");
		this.name = name;
		this.age = age;
	}
	// 省略name与age的setter和getter方法

	// name的setter和getter方法
	public void setName(String name)
	{
		this.name = name;
	}
	public String getName()
	{
		return this.name;
	}

	// age的setter和getter方法
	public void setAge(int age)
	{
		this.age = age;
	}
	public int getAge()
	{
		return this.age;
	}

	//	重写writeReplace方法，程序在序列化该对象之前，先调用该方法
	private Object writeReplace() throws ObjectStreamException
	{
		ArrayList<Object> list = new ArrayList<>();
		list.add(name);
		list.add(age);
		return list;
	}
}
```

Java序列化机制保证在序列化某个对象之前，先调用该对象的writePeplace方法，如果该对象返回另一个Java对象，则需系统转为序列化另一个对象。如下程序表明序列化Person对象，实际序列化的是ArrayList对象

```java

import java.io.*;
import java.util.*;

public class ReplaceTest
{
	public static void main(String[] args)
	{
		try (
			// 创建一个ObjectOutputStream输出流
			var oos = new ObjectOutputStream(new FileOutputStream("replace.txt"));
			// 创建一个ObjectInputStream输入流
			var ois = new ObjectInputStream(new FileInputStream("replace.txt")))
		{
			var per = new Person("孙悟空", 500);
			// 系统将per对象转换字节序列并输出
			oos.writeObject(per);
			// 反序列化读取得到的是ArrayList
			var list = (ArrayList) ois.readObject();
			System.out.println(list);
		}
		catch (Exception ex)
		{
			ex.printStackTrace();
		}
	}
}


```



综上所述，系统在序列化某个对象之前，首先调用该对象的writeReplcae方法，如果该方法返回另一个对象，系统将再次调用另一个对象的writeReplace方法，直到该方法不再返回另一个对象为止。然后程序最后调用该对象的writeObject方法保存对象状态。



与writeReplace方法相对应的是，序列化机制还有一个特殊的方法，它可以实现保护性复制整个对象，该方法是：

` ANY_ACCESS_MODIFIER Object readResolve() throws ObjectStreamException`

这个方法会在readObject方法调用后被调用，该方法的返回值会替代原来反序列化的对象，而原来使用readObject方法反序列化的对象会被丢弃。

readResolve方法在序列化单例类。枚举类时尤其有用。

例如下面一个枚举类：

```java
import java.io.*;

public class Orientation
	implements java.io.Serializable
{
	public static final Orientation HORIZONTAL = new Orientation(1);
	public static final Orientation VERTICAL = new Orientation(2);
	private int value;
	private Orientation(int value)
	{
		this.value = value;
	}
```

该枚举类构造器私有，程序有两个Orientation对象，但如果让该类实现Serializable接口，会引发一个问题，如果将Orientation.HORIZONTAL值序列化后再读入，如果立即拿读入后的对象与Orientation.HORIZONTAL进行比较，返回false，也就是说读入的对象是一个新的Orientation对象，而不等于Orientation类中的任何枚举类，虽然Orientation的构造器是私有的，但反序列化依然可以创建新的Orientation对象。

**提示**：

反序列化机制在恢复Java对象时无序调用构造器来初始化对象，从这个意义上看，序列化对象可以“克隆”对象。

在这种情况下，可以为Orientation类提供一个readResolve方法来解决，readResolve方法返回值会代替原反序列化对象，也就是让反序列化得到的Orientation对象直接被丢弃。

如下代码：

```java
	// 为枚举类增加readResolve()方法
	private Object readResolve() throws ObjectStreamException
	{
		if (value == 1)
		{
			return HORIZONTAL;
		}
		if (value == 2)
		{
			return VERTICAL;
		}
		return null;
	}
}
```

通过重写readResolve方法可以保证反序列化得到的依然是Orientation的两个枚举对象之一。



**注意**：所有单例类、枚举类序列化时都应该提供readResolve方法，这样才能保证反序列化的对象依然正常。

与writeReplace方法类似，readResolve方法一可以用任意访问控制符，因此父类readResolve方法可能被子类继承，这样使用该方法就会存在明显的缺点：当父类已经实现readResolve方法，子类将变得无从下手，如果父类包含protected或public的readResolve方法，而且子类没有重写该方法，会使得子类反序列化时得到一个父类的对象。总是让子类重写readResolve方法无疑是个负担。因此对于要作为父类继承的类而言，实现readResolve方法可能会有潜在危险。
**通常建议时，对于final类重写readResolve方法不会有任何问题，因为该类不可以被继承，否则重写readResolve尽量使用private修饰。**



## 另一种自定义序列化机制



Java提供了另一种序列化机制，即Externalizable接口，这种序列化方式完全由程序员决定存储和恢复对象数据，该接口定义了两个方法。

**void readExternal(ObjectInput in)**

功能：实现反序列化，从输入流中读取对象

参数：in:输入流

返回值：无

**注**

该方法调用DataInput的方法来恢复**基本类型**的实例变量值，调用ObjectOutput的writeObject()方法来保存**引用类型**的实例变量。

---

**void writeExternal(ObjectOutput out)**

功能：实现序列化

参数：out：输出流

返回值：无

**注**

该方法调用DataOutput的方法来保存**基本类型**的实例变量值，使用ObjectOutput的writeObject方法保存**引用类型**的实例变量值。

---

实际上，采用Externalizable接口序列化和前面的Serializable序列化很相似，只是Externalizable强制自定义序列化，必须要实现上述两个方法，下面的Person类实现了Externalizable接口，并且实现了该接口的两个方法。

```java
import java.io.*;

public class Person
	implements java.io.Externalizable
{
	private String name;
	private int age;
	// 注意必须提供无参数的构造器，否则反序列化时会失败。
	public Person(){}
	public Person(String name, int age)
	{
		System.out.println("有参数的构造器");
		this.name = name;
		this.age = age;
	}
	// 省略name与age的setter和getter方法

	// name的setter和getter方法
	public void setName(String name)
	{
		this.name = name;
	}
	public String getName()
	{
		return this.name;
	}

	// age的setter和getter方法
	public void setAge(int age)
	{
		this.age = age;
	}
	public int getAge()
	{
		return this.age;
	}

	public void writeExternal(java.io.ObjectOutput out)
		throws IOException
	{
		// 将name实例变量的值反转后写入二进制流
		out.writeObject(new StringBuffer(name).reverse());
		out.writeInt(age);
	}
	public void readExternal(java.io.ObjectInput in)
		throws IOException, ClassNotFoundException
	{
		// 将读取的字符串反转后赋给name实例变量
		this.name = ((StringBuffer) in.readObject()).reverse().toString();
		this.age = in.readInt();
	}
}
```

如果程序需要序列化实现Externalizable接口对象，一样调用writeObject方法，反序列化则调用readObject方法，和之前用法一样。

**注意**：

当时用Externalizable机制反序列化对象时，程序会首先使用public的无参数构造器创建实例，然后才执行readExternal方法进行反序列化，因此**实现Externalizable的序列化类必须提供public的无参数构造器**。





## 两种序列化机制对比

| 实现Serializable接口                                         | 实现Externalizable接口                             |
| ------------------------------------------------------------ | -------------------------------------------------- |
| 系统自动存储必要信息                                         | 程序员决定存储哪些信息                             |
| Java内建支持，易于实现，只需实现该接口即可，无需任何代码支持 | 仅提供两个空方法，实现该接口要为两个空方法提供实现 |
| 性能略差                                                     | 性能略好                                           |

虽然Externalizable接口性能较好，但是实现Externalizable接口导致编程复杂，所以但部分时候还是使用Serializable接口。



关于对象序列化，以下几点需要注意：

- 对象的类名、实例变量(包括基本类型、数组、对其他对象的引用)，都会被序列化，但是方法，类变量，transient实例变量(也被称为瞬态实例变量)不会被序列化
- 实现Serializable接口的类如果需要让某个实例变量不被序列化，应该添加transient关键词，而不应该用static修饰，虽然也可达到这个效果，但这个关键词不是这么用的，而且也会出现其他问题。
- 保证序列化对象的实例变量类型也是可序列化的。
- 反序列化对象必须有序列化对象的class文件
- 当通过文件、网络读取序列化后的对象时，必须按实际写入的顺序读取。



##　版本



反序列化对象时必须提供该对象对应类的class文件，现在问题是，随着项目升级，系统的class文件也会升级，Java如何保证class文件的兼容性。

Java序列化机制允许为序列化类提供一个private static final的serialVersionUID值，该类变量的值用于标识该Java类的序列化版本，也就是说，如果一个类升级以后，只要它的serialVersionUID值不变，序列化机制会把它们当成同一个序列化版本。

分配serialVersionUID值很简单，

`private static final long serialVersionUID = 512L;`

为了在反序列化时确保序列化版本的兼容性，最好在每个序列化类中加入这个类变量，具体数值可以自己定义，这样即使在某个对象被序列化后，它所对应的类被修改了，该对象也容易被正确反序列化。

每个类的serialVersionUID值都是唯一的，也就是说不同类的serialVersionUID值不应该相同。

如果不显式指定这个类变量的值，该类变量的值由JVM根据类的相关信息计算，而修改后的类的计算结果和之前的计算结果往往不同，造成对象反序列化因为类版本不兼容而失败。



不显式指定这个serialVersionUID值的另一个坏处是，不利于程序在不同JVM之间移植，因为不同编译器对该类变量的计算策略可能不同，从而造成类可能没有改变，但是serialVersionUID值不同。

那么对类的哪些修改可能会导致改变serialVersionUID值呢？

分三种情况讨论：

- 如果修改类时仅仅修改了静态变量或者瞬态实例变量，则反序列化不受任何影响，serialVersionUID值不会改变

- 如果修改类时，仅仅修改了方法，则反序列化不受任何影响，serialVersionUID值不改变

- 如果修改类时修改了非瞬态的实例变量，则可能导致序列化版本不兼容。

    1. 如果对象流中的对象与新类中包含同名的实例变量，而实例变量类型不同，则反序列化失败，serialVersionUID值会改变
    2. 如果对象流的对象比新类中包含更多的实例变量，即新类删除了部分实例变量，则多出的实例变量值被忽略，序列化版本可以兼容，serialVersionUID值不会改变
    3. 如果新类比对象流中的对象包含更多的实例变量，即新类增加了一些实例变量，那么序列化版本也可以兼容，serialVersionUID值不会更新，但是反序列化中的对象得到的新对象多出来的实例变量值都是null(引用类型变量)或者0(基本类型变量)。

    

    























