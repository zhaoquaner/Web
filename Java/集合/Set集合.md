# Set集合

Set集合不允许元素重复，如果向Set集合添加一个已经存在的元素，则会添加失败

## HashSet类

HashSet类是Set接口典型实现，大多时候使用Set时就是使用这个，HashSet按Hash算法来存储集合中的元素，因此存取和查找性能很好。

几个特点

- 不保证元素的排列顺序，顺序可能与添加顺序不一致
- HashSet不是同步的，如果有多个线程访问同一个HashSet，假设有两个或以上的线程同时修改了HashSet集合，必须通过代码保证同步
- **集合元素值可以是null**

当向HashSet集合添加元素时，HashSet会调用该对象的hashcode()方法得到hashcode值，然后依据这个值来决定存储位置，**如果有两个元素通过equals方法比较返回true，但是hashcode()方法返回值不相等，HashSet会把它们存储在不同位置，依然可以添加成功。**

即HashSet集合判断元素是否相等的标准是equals方法返回相等，并且hashcode()方法返回值也相等。

那么如果要重写对象对应类的equals方法，也应该重写对应的hashcode()方法，规则是：**如果两个对象通过equals方法返回true，那么hashcode()也应该返回值也应该相同。**

HashSet集合每一个能存储元素的位置通常称为“桶”，如果多个元素的hashcode值相等，但是equals方法返回false，那么就需要在一个“桶”里放多个元素，这会导致性能下降。

重写hashcode方法的基本原则

- 程序运行时，同一个对象多次调用hashcode()方法应该返回相同的值
- 两个对象通过equals比较返回true时，hashcode值也应该相同
- **对象中用作equals方法比较标准的实例变量，也应该用于计算hashcode值**

重写hashcode方法的一般步骤

1. 把对象内每个有意义的实例变量计算出一个int类型的hashcode值

2. 用第一步算出来的多个hashcode值组合成一个hashcode值返回

    为了避免直接相加产生偶然相等，可以使各实例变量的hashcode值乘以任意一个质数

**当把可变对象添加到HashSet中之后，不要再去修改该集合元素参与运算hashcode()、equals方法的实例变量**，否则会导致HashSet无法正确操作这些集合元素。



### LinkedHashSet类

它是HashSet类的子类，LinkedHashSet集合也使用hashcode值来决定存储位置，但它同时使用链表维护元素的次序，当遍历LinkedHashSet集合的元素时，LinkedHashSet会按元素的添加顺序来访问集合元素。

LinkedHashSet需要维护元素插入顺序，所以性能略低于HashSet性能

**注意：虽然使用链表来维护内部顺序，但是LinkedHashSet依然不允许元素重复。**







## TreeSet类

TreeSet类是SortedSet接口的实现类，TreeSet可以保证集合元素处于排序状态，与HashSet相比，TreeSet还提供了几个额外方法

- **Comparator comparator()**：如果TreeSet采用了定制排序，则该方法返回定制排序所使用的Comparator，如果TreeSet使用了自然排序，则返回null

- **Object first()**：返回集合第一个元素

- **Object last()**：返回集合最后一个元素

- **Object lower(Object e)**：返回集合中位于指定元素e之前的元素(即小于指定元素e的最大元素，e不一定是TreeSet里的元素)

- **Object higher(Object e)**：返回集合中位于指定元素e之后的元素(即大于指定元素e的最小元素，e不一定是TreeSet里的元素)

- **SortedSet subSet(Object fromElement, Object toElement)**：返回此Set的子集合，返回从formElement(包含)到toElement(不包含)

- **SortedSet headSet(Object toElement)**：返回此Set的子集合，由小于toElement的元素组成

- **SortedSet tailSet(Object fromElement)**：返回此Set的子集合，由大小或等于romElement的元素组成

    

    

TreeSet支持两种排序方式：自然排序、定制排序

默认情况，TreeSet使用自然排序。

### 自然排序

TreeSet会调用元素的compareTo(Object obj)方法来比较元素之间的大小关系，然后将集合元素按升序排列，这种方式就是自然排序。

Java提供了一个Comparable接口，**该接口定义了一个compareTo(Object obj)方法，该方法返回一个整数值，若该对象大于obj对象，返回正整数，小于obj对象返回负整数，等于返回0**

实现了该接口才可以比较大小，也才可以把对应类的对象放入TreeSet集合中

实现了Comparaable接口的常用类

- BigDecimal，BigInteger以及所有数组型对应的包装类
- Character：按字符Unicode值进行比较
- Boolean：true对应包装类实例大于false对应包装类实例
- String：依次比较字符串每个字符的Unicode值
- Date、Time：后面的时间大于前面的时间

**注意：因为只有相同类的实例才会比较大小，所有向TreeSet中添加的元素应该是同一类的对象，否则会抛出ClassCastException异常。**

**对于TreeSet来说，判断两个对象相等的唯一方法就是调用对象的compareTo()方法，返回0，则相等，否则不相等**

**当判断相等时，TreeSet无法把元素添加到集合中。**

这里应该注意一个问题，当重写该对象对应类的equals方法时，应该保证该方法与compareTo(Object obj)方法有一致的结果，即**如果两个对象通过equals方法返回相等时，这两个对象通过compareTo方法也应该返回0。**

**不要修改放入HashSet和TreeSet集合中元素的关键实例变量。**



### 定制排序

如果需要实现定制排序，则可以通过**Comparator**接口的帮助，该接口包含一个**int compare(T o1, T o2)**方法，如果o1大于o2，返回正整数，o1小于o2返回负整数，相等返回0

如果要实现定制排序，则在创建TreeSet集合对象时，需要提供一个Comparator对象与该集合关联，Comparator是一个函数式接口，可以用Lambda表达式来代替Comparator对象。

例子

```java
import java.util.*;

class M
{
	int age;
	public M(int age)
	{
		this.age = age;
	}
	public String toString()
	{
		return "M [age:" + age + "]";
	}
}
public class TreeSetTest4
{
	public static void main(String[] args)
	{
		// 此处Lambda表达式的目标类型是Comparator
		var ts = new TreeSet((o1, o2) ->
		{
			var m1 = (M) o1;
			var m2 = (M) o2;
			// 根据M对象的age属性来决定大小，age越大，M对象反而越小
			return m1.age > m2.age ? -1
				: m1.age < m2.age ? 1 : 0;
		});
		ts.add(new M(5));
		ts.add(new M(-3));
		ts.add(new M(9));
		System.out.println(ts);
	}
}
```

**注意：使用定制排序时，依然不能向TreeSet集合添加不同类的对象，否则会引发异常。**



## EnumSet类

EnumSet是一个专为枚举类设计的集合类，EnumSet中所有元素都必须是指定枚举类型的枚举值，该枚举类型在创建EnumSet时显式或者隐式的指定，EnumSet的集合元素也是有序的，EnumSet以枚举值在Enum类里定义顺序来决定集合元素的顺序。

**EnumSet集合不允许添加null元素**。

EnumSet没有暴露任何构造器，程序应该用它提供的类方法来创建EnumSet对象

- **EnumSet allOf(Class elementType)**：创建一个包含指定枚举类所有枚举值的EnumSet集合
- **EnumSet complementOf(EnumSet s)**：创建一个其元素类型与指定EnumSet里元素类型相同的EnumSet集合，该集合包含原EnumSet集合不存在的、枚举类剩下的枚举值(新EnumSet集合和旧EnumSet集合加起来就是枚举类所有的枚举值)
- **EnumSet copyOf(Collection c)**：使用一个普通集合来创建EnumSet集合,要求Collection所有元素必须是同一枚举类的枚举值
- **EnumSet copyOf(EnumSet s)**：创建一个与指定EnumSet具有相同类型相同集合元素的EnumSet集合
- **EnumSet noneOf(Class elementType)**：创建一个元素类型为指定枚举类型的空EnumSet
- **EnumSet of(E first,E....rest)**：创建一个包含一个或多个枚举值的EnumSet集合，传入的多个枚举值必须属于同一个枚举类
- **EnumSet range(E from, E to)**：创建一个从from枚举值到to枚举值范围内所有枚举值的EnumSet集合



## 各Set实现类的性能分析

HahsSet的性能总是比TreeSet好(特别是常用的添加查询元素等操作)，因为TreeSet需要额外的红黑树算法来维护元素次序，只有需要一个保持排序的Set时，才需要TreeSet，否则都应该用HashSet

LinkedHashSet需要维护链表，所有也比HashSet性能差一点，但遍历集合会更快

Set的三个实现类都是线程不安全的，如果有多个线程访问该集合，并且超过一个线程修改了该Set集合，则必须手动保证该Set集合的同步性。

