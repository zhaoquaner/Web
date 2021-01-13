# 4-value数据类型及操作命令

Redis一共有5种数据类型：

1. 字符串string：可以存储任何形式的字符串，包括二进制数据，序列化后的数据，JSON化后的数据，甚至是一张图片，最大512M
2. 哈希类型Hash：是一个string类型的field和value的映射表，适合存储对象
3. 列表list：简单的字符串列表，按照插入顺序排序，可以添加一个元素到头部(左边)或者尾部(右边)
4. 集合Set：是String类型的无序集合，集合成员唯一，不能出现重复数据
5. 有序集合zset：和集合set一样是string类型元素的集合，不允许重复成员。并且zset的每个元素会关联一个分数(分数可以重复)，redis按照分数为集合成员进行从小到大排序



## 字符串类型String

常用命令：

### set

语法：set key value

作用：新建一个key-value键值对，类型都为string

返回值：设置成功，返回OK

注意：如果库中已经有key这个名，那么新value会覆盖旧value

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101140021172.png" alt="image-20210101140021172" style="zoom:67%;" />



### setrange

语法：setrange key offset value

作用：用value替换key存储的值，从offset开始。如果key不存在，那么就当空白字符串，

返回值：修改后的字符串值长度



索引看作从1开始，那么offset就代表，从第offset后开始替换，不包括offset。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101142514988.png" alt="image-20210101142514988" style="zoom:67%;" />

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101142506797.png" alt="image-20210101142506797" style="zoom:67%;" />![image-20210101142645311](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101142645311.png)

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101142506797.png" alt="image-20210101142506797" style="zoom:67%;" /><img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101142645311.png" alt="image-20210101142645311" style="zoom:50%;" />





### mset

语法：mset key value [key value ....]      使用空格分隔

作用：同时设置一个或多个key-value

返回值为OK

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101142905033.png" alt="image-20210101142905033" style="zoom:67%;" />

### get

语法：get key

作用：获取key对应的value值

返回值：获取成功，返回对应value值，否则返回nil(可以理解为null)

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101140143662.png" alt="image-20210101140143662" style="zoom:67%;" />



### getrange

语法：getrange key start end

作用：获取key存储字符串值从start到end结束的子字符串。包括start和end。**索引从0开始，负数表示从字符串末尾开始，-1表示最后一个字符**

返回值：存在key，就返回指定子字符串；不存在，返回

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101141814824.png" alt="image-20210101141814824" style="zoom:67%;" />

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101141858495.png" alt="image-20210101141858495" style="zoom:67%;" />



### mget

语法：mget key [key...]   使用空格分隔

作用：获取所有给定的key的字符串值

返回值：包含所有给定key对应的value列表。其中如果key不存在，则返回nil



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101143142094.png" alt="image-20210101143142094" style="zoom:67%;" />

### incr

语法：incr key

作用：将key对应的value的值加1，当value值为数字类型时才可以使用，对非数字操作不行

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101140817603.png" alt="image-20210101140817603" style="zoom:67%;" />



### decr

和incr用法一样，这个是减1



### append

语法：append key value

用法：如果key存在，则将value追加到已存在的value后面；如果不存在，那么就创建对应key和value

返回值：返回追加后，字符串总长度

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101141106168.png" alt="image-20210101141106168" style="zoom:80%;" />

### strlen

语法：strlen key

作用：返回key存储的字符串的长度

返回值：如果key存在， 那么返回字符串值的长度；不存在，返回0

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101141328434.png" alt="image-20210101141328434" style="zoom:80%;" />





## 哈希类型Hash

常用命令：

### hset

语法：hset key filed value

作用：向哈希表key中添加field-value键值对，如果key不存在，则新建hash表，执行赋值；如果存在field，则覆盖旧值

返回值：

- 如果field是hash表中新field，且设置值成功，返回1
- 如果field已经存在，旧值覆盖新值，返回0

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101143825536.png" alt="image-20210101143825536" style="zoom:67%;" />









### hget

语法：hget key field

作用：获取哈希表key中给定域的值

返回值：如果key不存在或者field不存在返回nil，否则返回field对应的值

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101144022748.png" alt="image-20210101144022748" style="zoom:67%;" />



### hmset

语法：hmset key field value [field value...]

作用：同时将多个field-value键值对添加到哈希表key中，此命令会覆盖已存在的field。如果key不存在，那么创建空的hash表，再执行hmset

返回值：成功返回OK，失败返回错误

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101144247754.png" alt="image-20210101144247754" style="zoom:67%;" />



### hmget

语法：hmget key field [field...]

作用：获取哈希表key中多个给定field对应的值

返回值：返回和field顺序对应的值，如果field不存在，返回nil



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101144415762.png" alt="image-20210101144415762" style="zoom:50%;" />



### hgetall

语法：hgetall key

作用：获取哈希表key中所有的域和值

返回值：以列表形式返回哈希表中域和值，key不存在，返回空hash

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101144618780.png" alt="image-20210101144618780" style="zoom:67%;" />

### hdel

语法：hdel key field [field...]

作用：删除哈希表key中一个或多个指定field及其值。如果不存在field，则忽略

返回值：返回删除成功的field的数量



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101144801626.png" alt="image-20210101144801626" style="zoom:67%;" />



### hkeys

语法：hkeys key

作用：查看哈希表key中所有的field

返回值：包含所有field的列表，key不存在返回空列表

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101145107819.png" alt="image-20210101145107819" style="zoom: 50%;" />![image-20210101145125043](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101145125043.png)



### hvals

语法：hvals key

作用：返回哈希表中所有域对应的值

返回值：值的列表，key不存在，返回空列表

![image-20210101145125043](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101145125043.png)

### hexists

语法：hexists key field

作用：查看哈希表key中，指定的field是否存在值

返回值：存在返回1，不存在返回0

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101145150434.png" alt="image-20210101145150434" style="zoom:67%;" />





## 列表类型List

简单的字符串列表，也就是value只能是字符串。可以添加一个元素到列表的表头(左边)或者表尾(右边)

常用命令：



### lpush

语法：lpush key value [value..]

作用：将一个或多个值插入列表key的表头(左边)，从左边开始加入值，按从左到右顺序依次加入到表头。**如果不存在key，那么会创建一个list，并加入给定value。也就是说，如果给定key存在，那么列表至少有一个value。**

返回值：一个数字，新列表的长度

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101145717927.png" alt="image-20210101145717927" style="zoom:67%;" />

在列表中顺序依次为c b a



### lpushx

和lpush用法一样，只是当key不存在时，该命令不会创建list

### rpush

语法：rpush key value [value..]

作用：将一个或多个值插入列表key的表尾(右边)，从左边开始加入值，按从左到右顺序依次加入到表尾。**如果不存在key，那么会创建一个list，并加入给定value。也就是说，如果给定key存在，那么列表至少有一个value。**

返回值：一个数字，新列表的长度

`rpush list a b c`，那么在列表中顺序为a   b   c

### rpushx

和rpush用法一样，只是当key不存在时，该命令不会创建list

### lrange

语法：lrange key start stop

作用：获取列表key中指定区间的元素。索引从0开始。包括start和stop

也可以为负数，-1为列表最后一个元素，-2为倒数第二个元素，以此类推。start、stop超出列表范围不会出现错误。

返回值：指定区间的列表

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101150156030.png" alt="image-20210101150156030" style="zoom:67%;" />



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101150210479.png" alt="image-20210101150210479" style="zoom:67%;" />



### lindex

语法：lindex key index

作用：获取列表key中下标为index的元素。索引从0开始。也可以是负数，-1代表最后一个元素。

返回值：存在，则返回指定元素；index不再列表范围，返回nil

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101150644676.png" alt="image-20210101150644676" style="zoom:67%;" />



### llen

语法：llen key

作用：获取列表key的长度

返回值：数值，列表长度；不存在key则返回0



### lrem

语法：lrem key count value

作用：根据参数count的值，移除列表中和参数value相等的元素，count>0；则从列表左侧开始向右移除count个相等的元素；count<0，则从列表右侧开始移除 count绝对值 个相等的元素；count=0，则移除所有和value相等的值

返回值：移除的元素个数

### lset

语法：lset key index value

作用：将列表key下标为index的元素的值设置为value

返回值：成功返回OK；key不存在或者index超出范围返回错误信息



### linsert

语法：linsert key BEFORE|AFTER pivot value

作用：将值value插入到列表key中位于值pivot之前或之后的位置。key不存在或pivot不在列表，则不执行任何操作

返回值：执行成功，返回新列表长度；没有找到pivot返回-1；key不存在返回0

注意：pivot是值value，不是索引。



### lpop

语法：lpop key

作用：删除并获取列表key中第一个元素(最左边表头元素)

返回值：列表不存在，返回nil；否则返回对应元素值



### rpop

语法：rpop key

作用：删除并获取列表key中最后一个元素(最右边表尾元素)

返回值：列表不存在，返回nil；否则返回对应元素值



### blpop

语法：blpop key [key..] timeout 

作用：删除并获取列表key中第一个元素(最左边表头元素)，如果列表没有元素会阻塞到超时。超时时间timeout单位为秒

返回值：列表不存在，返回nil；否则返回一个含有两个元素的列表，第一个元素是列表的key，第二个元素是被删除的value



### brpop

语法：brpop key [key..] timeout 

作用：删除并获取列表key中最后一个元素(最右边表尾元素)，如果列表没有元素会阻塞到超时。超时时间timeout单位为秒

返回值：列表不存在，返回nil；否则返回一个含有两个元素的列表，第一个元素是列表的key，第二个元素是被删除的value







## 集合类型Set

Set是字符串类型的无序集合，元素不能重复

常用命令有：



### sadd

语法：sadd key member [member...]

作用：将一个或多个元素加入到集合key中；如果有member已经在集合中，则会被忽略，不会加入

返回值：加入到集合的新元素的个数，不包括被忽略的元素

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102113140552.png" alt="image-20210102113140552" style="zoom:67%;" />



### smembers

语法：smembers key

作用：获取集合key中所有的成员元素，不存在的key被视为空集合

返回值：成员元素列表

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102113244013.png" alt="image-20210102113244013" style="zoom:67%;" />



### sismember

语法：sismember key member

作用：判断member元素是否是集合key的成员

返回值：是返回1，不是返回0



### scard

语法：scrad key

作用：获取集合中元素个数

返回值：一个数字





### srem

语法：srem key member [member...]

作用：删除集合key中一个或多个元素，不存在的元素会被忽略

返回值：数字，成功删除的元素个数，不包括忽略的元素



### srandmember

语法：srandmember key [count]

作用：如果只提供key参数，那么会随机返回集合一个元素，元素不删除；如果提供了count，count是正数，那么返回包含了count个元素的集合，集合元素各不相同；如果是负数，返回一个count绝对值的长度的集合，集合中元素可能会重复多次

返回值：多个元素的集合



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102114027892.png" alt="image-20210102114027892" style="zoom:67%;" />

### spop

语法：spop key [count]

作用：随机从集合中删除并获取一个元素，count是删除元素的个数。不指定count，默认删除一个。

返回值：被删除的元素；如果key不存在或者空集合返回nil



### sinter

语法：sinter  key1 [key2...]

作用：返回给定所有元素的交集。如果只给一个集合参数，就返回该集合所有元素

返回值：一个交集合。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102114459556.png" alt="image-20210102114459556" style="zoom:67%;" />



### sinterstore

语法：sinter destination key1 [key2]

作用：返回给定所有集合的交集元素个数并存储到destination中

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102114733237.png" alt="image-20210102114733237" style="zoom:67%;" />





### sunion

语法：sunion key1 [key2]

作用：返回给定所有集合的并集元素，如果只给一个集合参数，就返回该集合所有元素。

返回值：一个集合





### sunionstore

语法：sunionstore destination key1 [key2]

作用：返回给定所有集合的并集元素个数并存储到destination中

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102114931584.png" alt="image-20210102114931584" style="zoom:50%;" />









## 有序集合类型zSet

有序集合zset和集合类型set都是string类型的集合。且不允许重复。

不同的是，zset每个元素都会关联一个分数，分数可以重复，分数可以是整数或浮点数

redis通过分数来为集合成员进行从小到大的排序。



常用命令有：



### zadd

语法：zadd key score member [score member...]

作用：将一个或多个member元素及其score值加入到有序集合key中，如果mmember存在集合中，则更新score值。

返回值：新添加的元素个数，不包括已存在的member





### zrange

语法：zrange ket start stop [WITHSCORES]

作用：查询有序集合指定区间的元素。集合成员按score值从小到大来排序。索引从0开始。-1表示最后一个成员。如果加上WITHSCORES选项，表示让score和value一起返回

返回值：自定区间的成员集合





### zrevrange

语法：zrevrange ket start stop [WITHSCORES]

作用：查询有序集合指定区间的元素。集合成员按score值从大到小来排序。索引从0开始。-1表示最后一个成员。如果加上WITHSCORES选项，表示让score和value一起返回

返回值：自定区间的成员集合

和zrange命令基本一样。zrange从小到大排列，zrevrange从大到小排列



### zrem

语法：zrem key member [member...]

作用：删除有序集合key中的一个元素或多个，不存在的成员会被忽略

返回值：被成功刹车农户的成员数量。不包括被忽略的成员





### zcard

语法：zcard key

作用：获取有序集key元素个数

返回值：元素个数；key不存在，返回0





### zrangebyscore

语法：zrangebyscore key min max [WITHSCORES] [LIMIT offset count]

作用：获取有序集key中，所有socre介于min和max之间(包括min和max)的成员，有序集合按递增(从小到大)排序

​			使用符号`(`表示不包括min和max，也就是开区间；

​			使用-inf和+inf分别表示，最小和最大；

​			**LIMIT用来限制返回结果的数量和区间。表示在符合条件的成员中，从第offset个位置(索引从0开始)开始，取count个元素。**

​			WITHSCORES表示一起显示score和value

返回值：指定区间的集合数据

使用的数据：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102120807849.png" alt="image-20210102120807849" style="zoom:50%;" />

第一个例子：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102120849877.png" alt="image-20210102120849877" style="zoom:50%;" />

使用`(`代表开区间：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102120937482.png" alt="image-20210102120937482" style="zoom:50%;" />

使用-inf和+inf表示最小和最大：



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102121032936.png" alt="image-20210102121032936" style="zoom:50%;" />

使用limit限制输出开始位置和个数：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102121129992.png" alt="image-20210102121129992" style="zoom:50%;" />

表示在符合条件的所有元素中，从第一个位置开始取，连续取2个元素。



### zrevrangebyscore

语法：zrevrangebyscore key min max [WITHSCORES] [LIMIT offset count]

作用：获取有序集key中，所有socre介于min和max之间(包括min和max)的成员，有序集合按递减(从大到小)排序

​			使用符号`(`表示不包括min和max，也就是开区间；

​			使用-inf和+inf分别表示，最小和最大；

​			**LIMIT用来限制返回结果的数量和区间。表示在符合条件的成员中，从第offset个位置(索引从0开始)开始，取count个元素。**

​			WITHSCORES表示一起显示score和value

返回值：指定区间的集合数据



和zrangebyscore基本一样，这个是从大到小排序。



### zcount

语法：zcount key min max

作用：返回有序集key中，score值在min和max之间(默认包括min和max)的成员数量。使用`(`代表开区间

返回值：元素个数



### zscore

语法：zscore key member

作用：返回有序集key中，给定member的分数值score

返回值：分数值，不存在返回nil



### zrank

语法：zrank key member

作用：返回有序集合key中指定成员索引，分数由小到大排列

返回值：索引值，不存在返回nil



### zrevrank

语法：zrevrank key member

作用：返回有序集合key中指定成员索引，分数由大到小排列

返回值：索引值，不存在返回nil





### zremrangebyscore

语法：zremrangebyscore key start stop

作用：删除指定分数区间的所有元素，默认包括start和stop，使用`(`可不包括，开区间

返回值：成功删除的元素个数



### zremrangebyrank

语法：zremrangebyrank key start stop

作用：删除指定排名索引区间的所有元素，包括start和stop。索引从0开始，排序由小到大。

返回值：成功删除的元素个数







