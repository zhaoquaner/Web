# 3-关于key的命令

Redis是key-value型数据库。所以有操作key的一类命令，同样也有操作value的一类命令。



## keys

语法：keys pattern

作用：查找所有符合模式pattern的key，pattern可以使用通配符。

通配符：

			1. *：表示0-多个字符，keys *代表查询所有key
			2. ?：表示单个字符，例如keys hel?o，可匹配hello，helqo等

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101131747885.png" alt="image-20210101131747885" style="zoom:80%;" />



## exists

语法：exists key [key..]

作用：判断指定的key或多个key是否存在。

返回值：返回存在key的数量，不管是查找一个key还是多个key

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101132026879.png" alt="image-20210101132026879" style="zoom:67%;" />



## expire

语法：expire key seconds

作用：设置key的生存时间，超过时间，key会自动删除，单位是秒

返回值：设置成功返回1，不成功是0



如果不设置，那么key会永久保存。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101132515408.png" alt="image-20210101132515408" style="zoom:67%;" />





## ttl

语法 ：ttl key

作用：返回key的剩余时间(time to live)，以秒为单位

返回值：

- 返回   -1：代表没有设置key的生存时间，永不过期
- 返回   -2 ：代表不存在该key
- 数字：key的剩余时间，秒为单位

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101132827883.png" alt="image-20210101132827883" style="zoom:67%;" />



## type

语法：type key

作用：查看key存储的value值得数据类型

返回值：使用字符串表示的数据类型

- none：key不存在
- string：字符串
- list：列表
- set 集合
- zset：有序集合
- hash：哈希表

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101133050493.png" alt="image-20210101133050493" style="zoom:67%;" />



## del

语法：del key [key..]

作用：删除存在的key，不存在的key忽略

返回值：数字，删除的key的数量

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101133229831.png" alt="image-20210101133229831" style="zoom:67%;" />





## persist

语法：persist key 

作用：移除key的过期时间，key将持久保持

返回值：成功返回1，失败返回0



## 其他命令

1. rangomkey

    语法：randomkey

    作用：从当前库随机返回一个key

    返回值：返回一个key

2. move 

    语法：move key db

    作用：将当前数据库中的指定key移动到数据库db中

    返回值：成功返回1，失败返回0

3. pttl

    语法：pttl key

    作用：以毫秒为单位，返回key的剩余过期时间

    返回值：

    - -1：没有设置key的过期时间，永久保存
    - -2：不存在该key
    - 数字：以毫秒为单位，key的剩余过期时间

4. rename

    语法：rename key newkey

    作用：重命名key为newkey

    返回OK代表成功

    当库中已存在newkey时，那么newkey对应的值将被覆盖，也就是newkey对应的value将是key对应的value

    <img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101134657570.png" alt="image-20210101134657570" style="zoom:67%;" />

5. renamenx

    语法：rename key newkey

    作用：当newkey不存在时，将key改名为newkey。也就是避免覆盖已存在的key对应的value

6. dump

    语法：dump key

    作用：序列为给定key

    返回值：若序列化成功，则返回被序列化的值。

    

    

    

    

    

    

    

    

    

    

    

    

    