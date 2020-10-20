#　Queue集合

Queue用于模拟队列这种数据结构，Queue接口定义了几个方法

- **void add(Object e)**：将指定元素加入到队列尾部
- **boolean offer(Object e)**：将指定元素添加到队列尾部，当使用容量有限的队列时，此方法比add更好一些
- **Object element()**：获取队列头部的元素，但是不删除该元素
- **Object peek()**：获取队列头部元素,但是不删除该元素，如果队列为空，返回null
- **Object poll()**：获取队列头部元素，并删除该元素，如果队列为空，返回null
- **Object remove()**：获取队列头部元素，并删除该元素

Queue接口有一个PriorityQueue实现类。除此之外Queue还有一个Deque子接口，Deque代表一个双端队列，可以从两端增加、删除元素，因此Deque的实现类既可以当成队列使用，也可以当成栈来使用。Deque有ArrayDeque和LinkedList两个实现类。



## PriorityQueue实现类

PriorityQueue是一个比较标准的队列实现类，之所以说是比较标准，而不是绝对标准，**是因为PriorityQueue保存队列元素并不是按加入队列的顺序，而是按队列元素的大小重新排序。**因此当使用peek或者poll取出队列元素时，并不是取出最先进入的元素，而是最小的元素。从这个意义上看，PriorityQueue已经违反了队列的最基本原则：先入先出

**PriorityQueue不允许插入null元素**

PriorityQueue有两种排序方式，自然排序和定制排序。和TreeSet一样，在这里不重复。





## Deque接口和ArrayDeque实现类



Deque接口是Queue的子接口，它代表一个双端队列，有一些操作元素的方法

- **void addFirst(Object e)**：将指定元素插入到双端队列的开头
- **boolean offerFirst(Object e)**：将指定元素插入到该队列的开头
- **void addLast(Object e)**：将指定元素插入到双端队列的末尾
- **boolean offerLast(Object e)**：将指定元素插入到该队列的末尾



- **Iterator descendingIterator()**：返回该双端队列对应的迭代器，它将以逆向顺序迭代队列

    

- **Object getFirst()**：获取但不删除队列第一个元素

- **Object getLast()**：获取但不删除队列的最后一个元素

    

- **Object peekFirst()**：获取但不删除该队列的第一个元素，如果队列为空，返回null

- **Object peekLast()**：获取但不删除该队列的最后一个元素，如果队列为空，返回null

- **Object pollFirst()**：获取并且删除该队列的第一个元素，如果队列为空，返回null

- **Object pollLast()**：获取并且删除该队列的最后一个元素，如果队列为空，返回null

    

- **Object pop() (栈方法)**：弹出该栈的栈顶元素，相等于removeFirst()

- **Object push(Object e) (栈方法)**：将元素e放入栈的栈顶，相等于addFirst()

- **Object removeFirst()**：获取并删除该双端队列第一个元素

- **Object removeFirstOccurence(Object e)**：删除该双端队列的第一次出现元素o

- **Object removeLast()**：获取并删除该双端队列最后一个元素

- **Object removeLastOccurence(Object e)**：删除该双端队列的最后一次出现元素o

Deque接口提供了一个典型实现类：ArrayDeque，它是一个基于数组实现的双端队列，创建Deque时可以指定numElements参数，如果不指定，默认为16



**Tips：ArrayList和ArrayDeque两个集合类实现机制基本类似，底层都使用一个动态的、可重分配的Object[]数组来存储集合元素，当超出数组容量时，系统会在底层重新分配一个Object[]数组来存储集合元素。**





## LinkedList实现类

LinkedList实现类是List接口的实现类,意味着它是一个List集合，可以根据索引随机访问集合中的元素，除此之外LinkedList还实现了Deque接口，可以被当成双端队列来使用。

LinkedList可以作为List集合、双端队列、栈来使用，说明LinkedList是一个功能很强大的集合类。

LinkedList内部以链表的方式保存元素，在随意访问集合元素时性能较差，但插入和删除时性能出色。



## 各种线性表的性能分析

数组使用连续内存区保存元素，因此在随即访问时性能最好，而内部以链表实现的集合在执行插入和删除时有较好性能。

但是总体来说，ArrayList比LinkedList性能更好，因此大部分时候都使用ArrayList

关于使用List集合有如下建议

- 如果需要遍历List集合，对于ArrayList、Vector集合，应该使用随机访问(get)来遍历集合元素，这样性能更好，对于LinkedList集合，采用迭代器Iterator遍历更好
- 如果需要经常执行插入删除操作，可以考虑使用LinkedList集合
- 如果有多个线程需要同时访问List集合中的元素，可以考虑使用Collections将集合包装成线程安全的集合。

