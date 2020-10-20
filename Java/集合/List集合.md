#　List集合

List集合代表一个元素有序可以重复的集合，每个元素有对应的顺序索引，从0开始。

List接口作为Collection的子接口，可以使用Collection的所有方法，除此之外，List集合还增加一个根据索引操作集合元素的方法

- **void add(int index, Object element)**：将元素插入到List集合的index处(index从0开始，例如插入第一个元素，index为0)
- **boolean addAll(int index, Collection c)**：将集合c包含的所有元素都插入到List集合的index处
- **Object get(int index)**：返回集合index处的元素
- **int indexOf(Object o)**：返回对象o在List集合第一次出现的位置索引
- **int lastIndexOf(Object o)**：返回对象o在List集合中最后一次出现的位置索引
- **Object remove(int index)**：删除并返回index索引处的元素
- **Object set(int index, Object element)**：将List集合index位置处的元素替换成element对象，返回被替换的旧元素
- **List subList(int fromIndex, int toIndex)**：返回从索引fromIndex(包括)到toIndex(不包括)处所有集合元素组成的子集合

所有的List实现类都可以调用这些方法

Java 8为List接口增加了两个新的默认方法

- **void replaceAll(UnaryOperator operator)**：根据operator指定的计算规则重新设置List集合中的所有元素
- **void sort(Comparator c)**：根据Comparator参数对List集合元素排序

**注意：使用indexOf、lastIndexOf和remove方法时，需要在集合中匹配和给定参数相同的元素，List集合判断相等的方式是只要equals方法返回true即可**。

sort方法需要一个Comparator对象来控制元素排序，可以用Lambda表达式；replaceAll方法需要一个UnaryOperator接口实现类，它也是一个函数式接口，可以使用Lambda表达式。

使用sort和replaceAll方法的例子

```java
import java.util.*;

public class ListTest3
{
	public static void main(String[] args)
	{
		var books = new ArrayList();
		// 向books集合中添加4个元素
		books.add("轻量级Java EE企业应用实战");
		books.add("疯狂Java讲义");
		books.add("疯狂Android讲义");
		books.add("疯狂iOS讲义");
		// 使用目标类型为Comparator的Lambda表达式对List集合排序
        //字符串越长，字符串越大
		books.sort((o1, o2) -> ((String) o1).length() - ((String) o2).length());
		System.out.println(books);
		// 使用目标类型为UnaryOperator的Lambda表达式来替换集合中所有元素
		// 该Lambda表达式控制使用每个字符串的长度作为新的集合元素
		books.replaceAll(ele -> ((String) ele).length());
		System.out.println(books); // 输出[7, 8, 11, 16]
	}
}
```

List集合额外提供了一个listIterator方法，该方法返回一个ListIterator对象，ListIterator接口继承了Iterator接口，提供专门操作List的方法，ListIterator还增加了一个方法

- **boolean hasPrevious()**：返回该迭代器关联的集合是否还有上一个元素
- **Object previous()**：返回该迭代器的上一个元素
- **void add(Object o)**：在指定位置插入一个元素





## ArrayList和Vector实现类

ArrayList和Vector作为List接口的实现类，完全支持List接口所有的功能

ArrayList和Vector实现类都是基于数组实现的List类，ArrayList和Vector封装了一个动态的、允许再分配的Object[]数组，

ArrayList和Vector对象使用initialCapacity参数来设置该对象的长度，当向ArrayList或Vector-添加元素超过了数组长度后，它们的initialCapacity会自动增加。

通常来说，我们无需关注initialCapacity，但是如果向ArrayList或Vector集合添加大量元素时，可以使用ensureCapacity方法一次性增加initialCapacity，可以减少重分配的次数，提高性能。

如果开始就知道要存储多少元素，可以在创建时指定initialCapacity参数，**如果不指定，默认长度为10。**

ArrayList和Vector提供了两个方法来重分配Object[]数组

- **void ensureCapacity(int minCapacity)**：将Object[]数组的大小增加，以确保它至少可以容纳由minCapacity参数指定的元素数。
- **void trimToSize()**：调整Object[]数组长度为当前元素的个数





ArrayList和Vector在用法上几乎完全相同，但是Vector是一个比较古老的集合，有一些很长的方法名，也有很多缺点，尽量少用Vector。

除此之外，ArrayList和Vector的一个显著区别是ArrayList是线程不安全的，程序必须手动保证该集合的同步性，Vector是线程安全的。

Coolections工具类可以使ArrayList变成线程安全的。

Vector还提供了一个子类Stack，它模拟“栈”这种数据结构，提供了几个方法

- Object peek():返回栈的第一个元素，但并不将该元素出栈
- Object pop()：返回栈的第一个元素，并将该元素出栈
- void push(Object item)：将元素入栈



##　固定长度的List

操作数组的工具类Arrays，该工具类提供了asList(Object ... a)方法，该方法可以把一个数组或指定个数的对象转换为一个List集合，**但是这个集合List即不是ArrayList实现类的实例，也不是Vector实现类的实例，而是Arrays的内部类ArrayList的实例。**

Arrays.ArrayList是一个固定长度的List集合，程序只能遍历该集合的元素，不可以增加和删除