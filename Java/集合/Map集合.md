# Map集合

Map集合用于保存具有映射关系的数据，因此Map集合保存两组值，一组是Map里的key，一组是Map里的value。两者都可以是任何引用类型的数据，**key不允许重复，即同一个Map对象的任意两个key通过equals方法比较总是返回false。**

如果把Map里所有key放在一起，他们就组成了一个Set集合(没有顺序，不能重复)。

Map有时候有人被称为字典或者关联数组。



Map类提供了一个Entry内部类来封装key-value对。



Map定义了以下方法：

**void clear**：删除所有key-value对

**boolean containsKey()**：查询Map中是否包含指定key，包含返回true

**boolean containsValue()**：查询Map中是否包含一个或多个value，包含返回true

**Set entrySet()**：返回Map中包含的key-value对所组成的Set集合，每个集合元素是一个Map.Entry对象

**Set keySet()**：返回该Map中所有key组成的Set集合

**Object get(Object key)**：返回指定key对应的value，如果此Map中不包含该key，返回null

**boolean isEmpty()**：查询该Map是否为空，如果为空返回true

**Object put(Object key, Object value)**：添加一个key-value对，如果当前Map中有一个与该key相等的key-value对，则																		新的会覆盖旧的

**void putAll(Map m)**：将指定Map中所有的key-value对复制到本Map中

**Object remove(Object key)**：删除指定key对应的key-value对，并返回，如果不存在返回null

**boolean remove(Object key, Object value)**：删除指定key和value对应的key-value对，删除成功返回true

**int size()**：返回该Map里的key-value对的个数

**Collection values()**：返回该Map里所有value组成的Collection

---

Java 8新增方法

**Object compute(Object key, BiFunction remappingFunction)**

该方法使用remappingFunction根据原来的key-value对计算出一个新的value。只要新value不为null，就使用新value覆盖旧value，返回新value；如果原value不为null，但新value为null，则删除原来key-value对，并返回null；如果原来(即不存在指定的key)、新的value都为null，就不改变任何key-value对，直接返回null。

---

**Object computeIfAbsent(Object key, Function remappingFunction)**

**如果传给该方法的key对应value为null，即Map集合不存在此key**，则根据key计算一个新的value，如果新value不为null，则替换旧value，并返回新value；如果新value为null，则删除原key-value对，直接返回null。(**如果指定key对应value不为null，直接返回对应value**)

---

**Object computeIfPresent(Object key, BiFunction remappingFunction)**

**如果传给该方法key参数在Map对应value不为null，即Map集合存在该key**，则该方法会用remappingFunction根据原key，value计算一个新value，如果计算结果不为null，则替换原来value，如果为null，则删除原来key-value对。(**如果传给方法的key对应value为null，即Map集合不存在value则直接返回nul**l)

---

**void forEach(BiConsumer action)**

该方法可以更简洁遍历Map的key，value对。

---

**Object getOrDefault(Object key, V defaultValue)**

获取指定key对应value，如果key不存在，返回defaultValue

---

**Object merge(Object key, Object value, BiFunction remappingFunction)**

该方法会先根据key参数获取该Map中对应的value，如果获取的value为null，则直接用传入的value覆盖原来的value，即添加一组新的key-value对；   如果获取的value不为null，则使用remappingFunction根据原value和新value计算新的结果，并覆盖原来value。

----

**Object putIfAbsent(Object key, Object value)**

该方法会自动检测指定key对应的value是否为null，如果为null，则会用新的value替换旧的value。并返回null，如果对应value不为空，则返回对应value。

----

**Object replace(Object key, Object value)**

将Map中指定的key对应的value替换成新的value，并返回旧的value。与传统put方法不同，如果不存在指定的key，那么不会添加新key-value对，而是直接返回null

---

**boolean replace(K key, V oldValue, V newValue)**

将Map中指定key-value对的原value替换成新value，如果找到指定key-value对，则执行替换并返回true，否则返回false

---

**void replaceAll(BiFunction function)**

使用function对原来的每组key-value对执行计算，并将计算结果作为该key-value对的value值

----





Entry是Map类的内部类，包含三个方法

- **Object getKey()**：返回该Entry里包含的key值
- **Object getValue()**：返回该Entry里包含的value值
- **Object setValue(V value)**：设置该Entry里包含的value，并返回新设置的value值



## HashMap和Hashtable实现类

HashMap和Hashtable都是Map接口的典型实现类，他们之间的关系类似于ArrayList和Vector。Hahstable是一个古老的Map实现类。尽量少使用Hahstable类。

HashMap和Hashtable有几个典型区别

- Hahstable是线程安全的，HashMap不是
- Hashtable不允许使用null作为key或者value，但HahsMap允许。

由于HashMap里的key不允许重复，所以HashMap最多有一个key-value对的key为null，但可以由无数多个key-value对的value为null。



HashMap和Hashtable不能保证其中key-value对的顺序。

HashMap和Hashtable判断两个key相等的标准是：两个key通过equals方法返回true，且hashcode值相同。

HashMap保存key的方式和HashSet保存集合元素的方式一样，所以都有同样的要求，即重写对应类的equals方法和hashcode时，应保持一致。



## LinkedHashMap实现类

LinkedHashMap实现类是HashMap的子类，LinkedHashMap实现类使用双向链表来维护key-value对的顺序，该链表负责维护Map的迭代顺序，这与插入顺序一致。

LinkedHashMap实现类因为使用链表来维护元素插入顺序，性能略低于HashMap，但在迭代访问所有元素时，有较好的性能。



## 使用Properites类读写属性文件

Properites类是Hashtable的子类，该对象在处理属性文件时很方便，Windows操作系统的ini文件就是一种属性文件。

Properites类可以把Map对象和属性文件关联起来，从而可以把Map对象中的key-value对写入属性文件，也可以把属性文件的”属性名=属性值“加载到Map对象中。由于属性文件的属性名和属性值都只能是字符串类型，所以Properites类的key和value都是字符串类型。

其实，Properites相当于一个key、value都是String的Map。

Properites类提供了几个方法

- **String getProperty(String key)**：获取Properites中指定属性名的属性值，类似于Map的get(Object key)方法
- **String getProperty(String key, String defaultValue)**：该方法与前一个基本类似，多一个功能是如果Properites中不存在指定key，则返回defaultValue
- **Object setProperty(String key, String value)**：设置属性值
- **void load(InputStream inStream)**：从属性文件中加载key-value对，把加载到的key-value对追加到Properites中
- **void store(OutputStream out, String comments)**：将Properites中的key-value对输出到指定的属性文件中



## SortedMap接口和TreeMap实现类

SortedMap接口有一个TreeMap实现类,TreeMap就是一个红黑树数据结构，每个key-value对作为一个节点，根据key对节点进行排序。

TreeMap有两种排序方式。

- 自然排序：TreeMap的所有key必须实现Comparable接口，该对象负责对TreeMap中所有的key进行排序，否则会抛出异常
- 定制排序：创建TreeMap时，传入一个Compatator对象，来负责对所有key进行排序，采用定制排序不要求Map的key实现Comparable接口

如果使用自定义类作为TreeMap的key，则重写该类的equals方法和comnpareTo方法时，应保持一致的结果。

TreeMap提供了一系列根据key顺序访问key-value对的方法

- **Map.Entry firstEntry()：**返回该Map中最小key对应的key-value对，如果Map为空，返回null
- **Map.Entry lastEntry()：**返回该Map中最大key对应的key-value对，如果Map为空，返回null
- **Map.Entry higherEntry() ：**返回该Map中位于key后一位的key-value对(即大于指定key的最小key对应的key-value对)，如果Map为空，返回null
- **Map.Entry lowerEntry()：**返回该Map中位于key前一位的key-value对(即小于指定key的最大key对应的key-value对)，如果Map为空，返回null
- **Object firstKey()：**返回该Map中最小的key值，如果该Map为空，返回null
- **Object lastKey()：**返回该Map中最大的key值，如果该Map为空，返回null
- **Object higherKey()：**返回该Map中位于key后一位的key值(即大于指定key的最小key值)，如果该Map为空，返回null
- **Object lowerKey()：**返回该Map中位于key前一位的key值(即小于指定key值的最大key值)，如果该Map为空，返回null
- **NavigableMap subMap(Object fromKey, boolean fromInclusive, Object toKey, boolean toInclusive)**：返回该Map的子Map，其key的范围是从fromKey到toKey,是否包含两个边界取决于每个参数后面的布尔参数
- **SortedMap subMap(Object fromKey, Object toKey)：**返回该Map的子Map，其key的范围是从fromKey(包括)到toKey(不包括)
- **SortedMap tailMap(Object fromKey)：**返回该Map的子Map，其key的范围是大于fromKey(包括)的所有key
- **NavigableMap tailMap(Object fromKey, boolean fromInclusive)**：返回该Map的子Map，其key的范围是大于fromKey(是否包含取决于第二个参数)的所有key
- **SortedMap headMap(Object toKey)**：返回该Map的子Map，其key的范围是小于toKey(不包括)的所有key
- **NavigableMap headMap(Object toKey, boolean inclusive)**：返回该Map的子Map，其key的范围是小于toKey(是否包含取决于第二个参数)的所有key





## WeakHashMap实现类

WeakHashMap实现类和HashMap的用法基本相似，与HashMap的区别在于，HashMap的key保留了对实际对象的强引用，这意味着只要该HashMap对象不被销毁，该HashMap的所有key所饮用的对象就不会被垃圾回收，HashMap也不会自动删除这些key对应的key-value对，但WeakHashMap的key只保留了对实际对象的弱引用，这意味着如果WeakHashMap的key所引用的对象没有被其他强引用变量所引用，则这些key所引用的对象可能被垃圾回收，WeakHashMap也可能自动删除这些key对应的key-value对

例子

```java
public  class  Main  {


        public  static  void  main(String[]  args) throws Exception {

                Map a = new WeakHashMap();

                a.put(new String("语文"), new String("良好"));
                a.put(new String("数学"), new String("及格"));
                a.put(new String("英语"), new String("中等"));

                a.put("物理", new String("超级棒的"));

                System.out.println(a);

                System.gc();

                System.runFinalization();

                System.out.println(a);

        }
}
```

运行结果

![image-20200504180133511](D:\各种文档\个人文档\学习总结\Java\集合\image\image-20200504180133511.png)



可以看出，当进行垃圾回收时，删除了WeakHashMap对象的前三个key-value对，因为添加前三个时，三个key都是匿名的字符串对象，WeakHashMap只保留了对他们的弱引用，这样垃圾回收时会自动删除。

WeakHashMap对象的第四个key-value对的key是一个字符串直接量，系统会使用缓冲池保留对该字符串对象的强引用，所以垃圾回收时不会回收它。





## IdentityHashMap实现类

这个Map实现类的机制和HashMap基本相似，但它在处理两个key相等时比较独特，在IdentityHashMap中，仅当两个key严格相等(key1 == key2)时，IdentityHashMap才会认为他们相等。

对于普通的HashMap，只要key1和key2通过equals方法返回true并且hashcode值相等即可。



## EnumMap实现类

EnumMap是一个和枚举类一起使用的Map实现，EnumMap中的所有key都必须是单个枚举类的枚举值，创建EnumMap时必须显式或者隐式的指定它对应的枚举类，EnumMap具有如下特征

- EnumMap在内部以数组形式保存，这种实现形式紧凑高效
- EnumMap根据key的自然顺序(即枚举值在枚举类的定义顺序)来维护key-value对的顺序
- EnumMap不允许以null作为key，但允许使用null作为value，





## 各Map实现类的性能分析

对于Map常用实现类来说，HashMap和Hashtable实现机制几乎一样，但是由于Hashtable是一个古老的线程安全的集合，所以HashMap比Hashtable要快

TreeMap通常比HashMap和Hashtable要慢，因为TreeMap使用红黑树来维护key-value对的顺序。

对于一般的应用场景，程序应该多考虑使用HashMap。

LinkedHashMap比HashMap慢一点，因为它需要链表来维护顺序。

IdentityHashMap性能没有出色之处，只是它使用==而不是equals方法来判断元素相等

## 

## HashSet和HashMap的性能选项

HashSet和HashMap都采用hash算法来绝对元素存储位置。

hash表中可以存储元素的位置称为"桶"，通常情况下，单个"桶"存储一个元素，此时性能是最好的。但是hash表的状态是open的，在发生hash冲突的情况下，单个桶会存储多个元素，这些元素以链表形式存储，必须按顺序存储。

HashSet和HashMap的hash表有一些属性

- 容量(capacity)：hash表中桶的数量
- 初始化容量(initial capacity)：创建hash表时桶的数量，HashSet和HashMap都允许在构造器中指定初始化容量
- 尺寸(size)：当前hash表中记录的数量
- 负载因子(load factor)：等于**$\frac{size}{capacity}$,**负载因子为0，表示空的hash表。

除此之外，hash表还有一个负载极限，它是一个0~1之间的数，决定了hash表的最大填满程度，当hash表的负载因子达到最顶负载极限时，hash表会自动成倍的增加容量，并将原有对象重新分配，这称为rehashing。

HashSet和HashMap的构造器都允许指定一个负载极限。HashSet、HashMap和Hashtable的默认负载极限时0.75。

较高的负载极限可以降低hash表占用的内存，但会增加查询数据的时间开销；较低的负载极限会提高查询数据的性能，但会增加内存消耗。



