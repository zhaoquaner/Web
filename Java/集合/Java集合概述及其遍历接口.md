# Java集合概述及其遍历接口

集合主要负责保存其他数据，集合和数组不一样，数组元素可以是基本类型如int，float，但集合里只能保存对象(实际保存的是对象的引用变量)。

Java集合类由两个接口派生而出：Collection和Map，这两个接口又包含了一些子接口和实现类。

继承树为

<img src="D:\各种文档\个人文档\学习总结\Java\集合\image\集合接口.png" style="zoom:150%;" />



大致分为四种集合：Set、List、Queue和Map

特点

- Set是无序集合，元素不能重复
- List是有序集合，每个元素有对应索引，元素可以重复
- Map保存的数据都是key-value对，即每项数据由两个值组成



## Collection接口

Collection接口是List、Set和Queue接口的父接口，它包含了几个操作集合元素的方法。



- **boolean add(Object o) **：向集合添加一个元素，添加成功返回true
- **boolean addAll(Collection c)**：把集合c的所有元素添加到指定集合中，如果添加成功返回true
- **void clear()**：清除集合所有元素
- **boolean contains(Object o)**：判断集合是否包含指定元素，包含返回true
- **boolean containsAll(Collection c)**：判断集合是否包含c中所有元素，是返回true
- **boolean isEmpty()**：判断集合是否为空，是返回true
- **Iterator iterator()**：返回一个Iterator对象，用于遍历集合元素
- **boolean remove(Object o)**：从集合中删除指定元素o，如果集合包含多个元素o时，只删除第一个符合条件的元素
- **boolean removeAll(Collection c)**：从集合删除集合c中不包含的元素(相等于该集合和c集合求交集)
- **int size()**：返回集合里元素个数
- **Object[] toArray()**：该方法把集合转换成一个数组，所有集合元素变成对应数组元素



## 使用Iterator遍历集合元素

Iterator接口时Collection的父接口，Iterator接口主要用来遍历Collection集合中的元素，Iterator对象也被称为迭代器。有四个方法

- **boolean hasNext()**：如果被迭代的集合元素还没有被遍历完，返回true
- **Object next()**：返回集合下一个元素
- **void remove()**：删除集合中上一次next方法返回的元素
- **void forEachRemaining(Consumer action)**：使用Lambda表达式遍历集合元素

Iterator仅仅用于遍历集合，如果需要创建Iterator对象，则必须有一个需要被迭代的集合。

例子

```java
import java.util.*;

public class IteratorTest
{
	public static void main(String[] args)
	{
		// 创建集合、添加元素的代码与前一个程序相同
		var books = new HashSet();
		books.add("轻量级Java EE企业应用实战");
		books.add("疯狂Java讲义");
		books.add("疯狂Android讲义");
		// 获取books集合对应的迭代器
		var it = books.iterator();
		while (it.hasNext())
		{
			// it.next()方法返回的数据类型是Object类型，因此需要强制类型转换
			var book = (String) it.next();
			System.out.println(book);
			if (book.equals("疯狂Java讲义"))
			{
				// 从集合中删除上一次next方法返回的元素
				it.remove();
			}
			// 对book变量赋值，不会改变集合元素本身
			book = "测试字符串";   // ①
		}
		System.out.println(books);
	}
}
```

当使用Iterator遍历集合时，Iterator并不是把集合元素本身传给迭代变量，而是把对应的值传给迭代变量，所以修改迭代变量的值对集合本身没有影响。

虽然remove方法可以删除集合元素，但尽量不要使用remove来删除集合元素。



### 使用Iterator接口的新增方法来遍历集合

Iterator接口有一个默认方法forEach(Consumer action)，该方法参数是一个函数式接口，可以使用Lambda来遍历，因为Iterator接口是Collection的父接口，所有Collection可以直接调用该方法。

当调用该方法时，程序会依次将集合元素传给Consumer的accept(T t)方法。

例子

```java
import java.util.ArrayList;
public  class  Main  {

        public  static  void  main(String[]  args) throws Exception {

                var c = new ArrayList();

                c.add("你好");
                c.add("同学");
                c.add("再见");
                
                c.forEach(obj->{System.out.println(obj);});

                
        }
}
```

Iterator接口同样有一个forEachRemaining(Consumer action)，该方法所需参数是函数式接口，当程序调用Iterator的该方法时，程序会把集合元素传给Consumer的accpet(T t)方法

例子

```java
public  class  Main  {

        public  static  void  main(String[]  args) throws Exception {

                var c = new ArrayList();

                c.add("你好");
                c.add("同学");
                c.add("再见");

                Iterator a = c.iterator();

                a.forEachRemaining(obj->{System.out.println(obj);});

        }
}
```



## 使用foreach循环来遍历集合元素

可以直接使用for each循环来遍历集合，更加方便

```java
public  class  Main  {

        public  static  void  main(String[]  args) throws Exception {

                var c = new ArrayList();

                c.add("你好");
                c.add("同学");
                c.add("再见");

                for(var obj : c)
                {
                        System.out.println(obj);
                }

        }
}
```

使用foreach循环遍历集合时也不能修改集合元素



## 使用Predicate操作集合

Collection集合有一个removeIf(Predicate filter)方法，该方法会批量删除符合filter条件的所有元素

该方法需要一个Predicate对象来作为参数，Predicate是函数式接口，可以用Lambda表达式

该接口中的唯一抽象方法test，如果符合filter条件，该方法返回true，否则false

例子

```java
public  class  Main  {

        public  static  void  main(String[]  args) throws Exception {

                var c = new ArrayList();

                c.add("你好");
                c.add("同学");
                c.add("再见");
                c.add("9dorgja[we0uf");
                c.add("sidufhsd");

                c.removeIf(e->{return ((String)e).length() > 10;});

                System.out.println(c);
                
        }
}
```

上述代码会把c集合里长度大于10的字符串删掉。

其中  `c.removeIf(e->{return ((String)e).length() > 10;})`的参数e可以用任意名称代替



##　使用Stream操作集合

Stream是一个通用流接口，IntStream,LongStream,DoubleStream则代表元素类型为int、long、double的流。

Java为每个流提供对应的Builder，例如Stream.Builder等等，可以用这些Builder创建对应的流

独立使用Stream步骤为

1. 使用Stream或XxxStream的builder类方法创建对应Builder
2. 重复调用Builder的add方法向该流添加元素
3. 调用Builder的build方法获取对应Stream
4. 调用Stream的聚集方法

```java
public  class  Main  {

        public  static  void  main(String[]  args) throws Exception {

                IntStream.Builder a = IntStream.builder();

                a.add(4);
                a.add(85);
                a.add(14);
                a.add(-15);
                a.add(-45);
                a.add(26);

                IntStream b = a.build();

                // 下面调用聚集方法的代码每次只能执行一个

		System.out.println("is所有元素的最大值：" + b.max().getAsInt());
		//System.out.println("is所有元素的最小值：" + b.min().getAsInt());
		//System.out.println("is所有元素的总和：" + b.sum());
		//System.out.println("is所有元素的总数：" + b.count());
		//System.out.println("is所有元素的平均值：" + b.average());

		System.out.println("is所有元素的平方是否都大于20:"
			+ b.allMatch(ele -> ele * ele > 20));

		System.out.println("is是否包含任何元素的平方大于20:"
			+ b.anyMatch(ele -> ele * ele > 20));

                // 将is映射成一个新Stream，新Stream的每个元素是原Stream元素的2倍+1

                var newIs = b.map(ele -> ele * 2 + 1);

                // 使用方法引用的方式来遍历集合元素
                newIs.forEach(System.out::println); // 输出41 27 -3 37
                
        }
}
```

上面聚集方法只能执行一行，需要把其他聚集操作注释掉。

Stream提供了很多方法来进行操作，分为“中间方法”和“末端方法”

- 中间方法：中间操作允许流保持打开状态，并允许直接调用后续的方法，中间方法的返回值是另外一个流
- 末端方法：末端方法是对流的最终操作，**当对某个Stream执行末端方法后，该流会被“消耗”且不能再使用**

除此之外关于流的方法有两个特征

- 有状态的方法：这种方法会给流增加一些新的属性，比如元素唯一性、元素的最大数量等等，有状态的方法往往需要更大的性能开销
- 短路方法：短路方法可以尽早结束对流的操作，不必检查所有元素

Stream常用中间方法(全部返回一个新的流)

- **filter(Predicate predicate)**：过滤所有不符合predicate的元素
- **mapToXxx(ToXxxFunction mapper)**：使用ToXxxFunction对流元素执行一对一转换，返回的新流包含转换生成的所有元素
- **peek(Consumer action)**：依次对每个元素执行一些操作，返回的流与原有流包含元素相同，主要用于调试
- **distinct()**：用于排序流中所有重复的流
- **sorted()**：用于保证流中所有元素在后序访问中处于有序状态

Stream常用末端方法

- **forEach(Consumer action)**：遍历流中所有元素，对每个元素执行action
- **toArray()**：将流中所有元素转换为一个数组
- **reduce()**：有三个重载版本，都是通过某种操作合并流中元素
- **min()**：返回流中最小值
- **max()**：返回流中最大值
- **count()**：返回所有元素数量
- **anyMatch(Predicate predicate)**：判断流中是否至少有一个元素符合Predicate条件
- **allMatch(Predicate predicate)**：判断流中是否所有元素符合Predicate条件
- **noneMatch(Predicate predicate)**：判断流中是否所有元素都不符合Predicate条件
- **fndFirst()**：返回流中第一个元素
- **findAny**：返回流中任意一个元素

Java允许使用这些流来操作集合，Collection提供了一个stream默认方法，该方法返回该集合对应流，然后可以使用流的方法来操作集合元素

例子：对集合元素进行批量操作

```java
import java.util.*;
import java.util.function.*;

public class CollectionStream
{
	public static void main(String[] args)
	{
		// 创建books集合、为books集合添加元素的代码与8.2.5小节的程序相同。
		var books = new HashSet();
        
		books.add("轻量级Java EE企业应用实战");
		books.add("疯狂Java讲义");
		books.add("疯狂iOS讲义");
		books.add("疯狂Ajax讲义");
		books.add("疯狂Android讲义");
        
		// 统计书名包含“疯狂”子串的图书数量
        
		System.out.println(books.stream()
			.filter(ele->((String) ele).contains("疯狂"))
			.count()); // 输出4
        
		// 统计书名包含“Java”子串的图书数量
		System.out.println(books.stream()
			.filter(ele->((String) ele).contains("Java") )
			.count()); // 输出2
        
		// 统计书名字符串长度大于10的图书数量
		System.out.println(books.stream()
			.filter(ele->((String) ele).length() > 10)
			.count()); // 输出2
        
		// 先调用Collection对象的stream()方法将集合转换为Stream,
		// 再调用Stream的mapToInt()方法获取原有的Stream对应的IntStream
		books.stream().mapToInt(ele -> ((String) ele).length())
			// 调用forEach()方法遍历IntStream中每个元素
			.forEach(System.out::println);// 输出8 11 16 7 8
	}
}
```

