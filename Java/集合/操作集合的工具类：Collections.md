# 操作集合的工具类：Collections

Java提供了操作Set、List和Map等集合的工具类Collections,提供了大量的方法对集合元素排序、查询修改等操作，还可以将集合元素设置为不可变，对集合对象实现同步控制。



## 排序操作

常用类方法来排序

- **void reverse(List list)**：反转指定集合元素的顺序
- **void shuffle(List list)**：对集合的元素进行随机排序
- **void sort(List list)**：根据元素自然顺序对指定list集合的元素按升序排序
- **void sort(List list, Comparator c)**：根据指定Comparator产生的顺序对List集合元素进行排序
- **void swap(List list, int i, int j)**：将指定List集合中的i处元素按升序进行排序
- **void rotate(List list, int distance)**：当distance为正数时，将list集合的后distance个元素整体移到前面；distance为负数时，将list集合的前distance个元素整体移到后面，该方法不会改变集合长度



## 查找替换操作

Collections提供的类方法用于查询替换集合元素

- **int binarySearch(List list, Object key)**：使用二分搜索法搜索指定的List集合，以获得指定对象在List集合中的索引，如果要使用此方法，则List集合元素必须已经处于有序状态
- **Object max(Collection coll)**：根据元素的自然顺序，返回给定集合的最大元素
- **Object max(Collection coll, Comparator comp)**：根据Comparator给定的顺序，返回给定集合中最大元素
- **Object min(Collection coll)**：根据元素的自然顺序，返回给定集合的最小元素
- **Object min(Collection coll, Comparator comp)**：根据Comparator给定的顺序，返回给定集合中最小元素
- **void fill(List list, Object obj)**：使用指定元素替换指定集合中所有元素
- **int frequency(Collection c, Object o)**：返回指定集合中指定元素出现的次数
- **int indexOfSubList(List source, List target)**：返回子List对象target在父List对象source第一次出现的位置索引
- **int lastIndexOfSubList(List source, List target)**：返回子List对象target在父List对象source最后一次出现的位置索引
- **boolean replaceAll(List list, Object oldVal, Object newVal)**：使用一个新值替换List对象的所有旧值



## 同步控制

Collections提供了多个synchronizedXxx()方法，可以把指定集合包装成线程同步的集合，从而解决多线程并发访问集合时的线程安全问题。

Java常用的集合框架的实现类HashSet,TreeSet, ArrayList, ArrayDeque, LinkedList, HashMap和TreeMap都是线程不安全的。

可以使用`ArrayList a = Collections.synchronizedList(new ArrayList());`将新创建的集合对象传给Collections的synchronizedXxx方法，获取对应的线程安全版本。



## Java 9新增的不可变集合

以前假如要创建一个包含6个元素的Set集合，程序需要先创建Set集合，然后调用6次add()方法，Java 9进行简化，程序直接调用Set、List、Map的of()方法即可创建一个包含N个元素的不可变集合。

不可变集合意味着不能向集合中添加元素也不能从集合中删除元素。

例子：创建一个Set、List的不可变集合

`var a = Set.of("java", "C", "C++");`  `var b = List.of("13, 56, 62");`

上述代码分别创建了三个元素的Set集合和List集合。