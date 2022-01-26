# 5-Redis高级话题



## Redis事务

事务是指一系列操作步骤，这些操作步骤，要么全都执行，要么全都不执行。



Redis中的事务是一组命令的集合，至少有两个命令。redis事务保证这些命令被执行时中间不会中断。

Redis事务相关的命令：

1. multi：标记一个事务的开始。事务内的多条命令不会立即执行，而是会按照先后顺序放入一个队列中。总是返回OK
2. exec：执行所有事务块的内容。可以理解为提交事务。事务被打断返回nil，否则返回OK
3. discard：取消事务，放弃执行事务块中的所有命令。总是返回OK
4. watch：语法是：watch key [key..]，监视一个或多个key，如果在事务执行之前这些key被其他命令改动，那么事务被打断。总是返回OK。
5. unwatch：取消watch命令对所有key的监视，如果在执行了watch命令后，exec命令或discard命令先被执行了话，就不需要执行unwatch了。总是返回OK



举几个例子：

### 事务正常执行

首先使用multi来标志事务开始，即告诉redis要开启一个事务，接下来输入的命令不要执行，先放到一个队列中。

然后使用exec来提交事务。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102123859889.png" alt="image-20210102123859889" style="zoom:67%;" />

### 事务被放弃

如果出现了命令的语法错误或者其他严重错误，导致服务器不能正常工作(例如内存不足)，那么Redis会检测到，并放弃事务。

一般都是语法出现错误，其他错误不常见。

例如使用incr给key对应的value值加1，incr只有一个参数，如果有多个参数，就会有语法错误。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102124525031.png" alt="image-20210102124525031" style="zoom:67%;" />

可以看到，因为之前出现的语法错误，事务已经被放弃。



### 执行出错，不会放弃事务

还有一种错误，只有在执行时才能发现，例如使用集合命令去操作字符串，incr命令操作不是数值的value等等。

这一类错误，Redis事务在执行到的时候，会报错，但是不会放弃事务。依然会执行其他事务。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102125059072.png" alt="image-20210102125059072" style="zoom:67%;" />



Redis在遇到这类错误，不会回滚事务，依然正常执行。这样可以保持Redis的高性能。



### 主动放弃事务

在开启事务后，如果想放弃执行命令，可以使用discard命令放弃事务。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102125432602.png" alt="image-20210102125432602" style="zoom:50%;" />





### Redis的watch机制

watch机制原理：

​		使用watch机制监视一个或多个key，如果有key的value值在事务提交之前被修改了，那么整个事务会被取消。执行exec时，会返回提示信息，表示事务已经失败。

​		watch机制，使的事务exec变的有条件，事务只有在watch监视的key对应value没有修改的情况下才会执行。如果使用watch监视了一个带过期时间的键，那么即时这个键过期了，事务仍然可以正常执行。

​			大多数情况下，不同客户端访问不同的键，相互同时竞争同一key的情况很少。

那么何时取消key的监视：

1. watch命令可以调用多次，对键的监视从watch执行后开始生效，一致到调用exec为止，不管事务是否成功执行，对所有键的监视都会被先取消
1. 当客户端断开连接时，该客户端对键的监视也会被取消
1. unwatch命令可以手动取消对所有键的监视





一个例子：

开启两个客户端，在A客户端开启事务，修改str1的值，并监视str1的值，然后在exec执行之前，在B客户端修改str1的值，会看到在A客户端执行提交事务exec后，返回错误信息。

A客户端：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102130604633.png" alt="image-20210102130604633" style="zoom:50%;" />



B客户端：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102130637145.png" alt="image-20210102130637145" style="zoom:50%;" />



A客户端执行exec后：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102130656183.png" alt="image-20210102130656183" style="zoom:50%;" />

返回nil，说明没有成功执行。



## 持久化

持久化可以理解为存储数据，到一个不会丢失的地方。因为如果把数据放在内存中，电脑关闭或者重启就会丢失数据。所以放在内存中的数据不是持久化的，放在磁盘就算是持久化。

Redis提供了两种机制来对数据进行持久化存储。

### RDB方式

Redis DataBase(RDB)，就是在指定的时间间隔内将内存中的数据集快照(可以理解为数据的情况)写入磁盘，数据恢复时，将快照文件读入内存即可。

RDB保存了在某个时间点的数据集，就是当前时间点的全部数据。存储在一个二进制文件中，默认是dump.rdb。

RDB适合做备份，可以保存最近一个小时，一天的全部数据。保存数据是在单独进程写文件，不影响Redis正常使用，并且RDB恢复数据时，比AOF快。



#### 实现RDB

使用RDB实现数据持久化，只需要在Redis的配置文件中配置即可。默认配置时启动的。

在配置文件中搜索SNAPSHOTTING。

有三处需要配置：

1. 配置执行RDB生成快照的时间策略。

    配置格式：save seconds changes

    意思是，当在seconds秒内，至少有changs个key改动后，那么就保存一次数据集。

    例子：save 20 4

    意思是，当在二十秒内，有4个及以上的key被改动后，就保存数据集

2. dbfilename：设置RDB的文件名，默认dump.rdb

3. dir：指定RDB文件存储位置，默认是`./`当前目录。就是启动redis的工作目录。如果删除dump.rdb文件，然后在windows下的C:/user/username下重新启动redis-server，那么dump文件就会保存到C:/user/username下。



一个例子：
修改配置文件

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102132454044.png" alt="image-20210102132454044" style="zoom:50%;" />

加上一个`save 20 4`，如果取消`save ""`的注释，代表不启用RDB。

其他不修改。

重新开启redis服务端，重新读取配置文件



#### 总结

RDB优点：存储的是数据快照文件，恢复数据方便。

缺点：

- 会丢失最后一次快照以后更改的数据。如果不能容忍一定数据丢失，使用RDB不是很好
- 由于需要经常操作磁盘，RDB会分出一个子进程，如果redis数据库很大，子进程会占用较多时间。



### AOF方式

Append-only File(AOF)，当Redis每收到一条改变数据的命令时，就把该命令追加写入到AOF文件中(只记录写操作，例如添加key，修改key对应value值，不记录读操作)，当Redis重启时，就读取AOF文件，把存储的所有命令重新执行一遍，来恢复数据。

#### 实现AOF

要在配置文件中配置对应项：

1. appendonly：意思是是否开启AOF持久化，默认是no，改为yes即开启
2. appendfilename：指定AOF文件名，默认是appendonly,aof
3. dir:指定AOF存放的目录默认是`./`
4. appendfsync：配置向AOF文件写命令数据时的策略，有以下可取值：
    - no：不主动进行同步操作，完全交给操作系统来做(每30秒一次)，比较快但是不很安全
    - always：每次执行写入都会执行同步，慢一些，但是较为安全
    - everysec：每秒执行一次同步操作，比较平衡。**默认项。**
5. auto-apf-rewrite-min-size：允许重写的最小AOF文件大小，默认64M，当AOF文件大小64M时，开始整理AOF文件，去掉无用的操作命令，减小aof文件大小。



#### 总结

AOF文件在操作过程中会变得越来越大，例如，输入一百次加法计算，最后数据库内存中只会存储最后的结果，但是AOF文件会存储100条记录，99条都对最终记录没用。但是Redis支持在不影响服务前提下，在后台重构AOF文件，让文件变小。



可以同时使用RDB和AOF两种方式，Redis默认优先加载AOF文件，因为aof数据最完整。





## 主从复制

通过持久化功能，Redis保证了即使服务器重启的情况下也不会丢失数据。但是数据只存储在一台服务器，如果这台服务器出现故障，那么数据同样会丢失。

为了避免单点故障，需要将数据赋值多份部署到不同的服务器上，即使一台服务器出现故障其他服务器依然可以继续提供服务。

这就要求当一台服务器上的数据更新后，自动将更新的数据同步到其他服务器上，通过Redis的主从复制来实现。



Redis提供了复制功能来自动实现多台redis服务器的数据同步，我们不需要关心如何复制，Redis会自动完成。

只要关心哪些服务器是从服务器，哪个是主服务器。



需要在配置文件中来指定多台服务器的主从关系。

**主服务器负责写入数据，同时将写入的数据实时同步给从服务器。**主服务器可以读取、写入数据。

**从服务器只能读取数据，不能写入数据。**

这种模式叫做主从复制，master/slave，主服务器是master，从服务器是slave。



默认master用于写数据，slave用于读数据。在slave中写入数据会出现错误。





Redis主从复制有两种实现方法：

- 修改配置文件，在启动时，服务器读取配置文件，并自动成为指定服务器的从服务器，构成主从复制关系
- 在启动redis服务时指定当前服务 成为某个主Redis服务器的从服务器。



一般常用第一种方式，即修改配置文件。

主服务器不需要修改，在从服务器中加上slaveof，格式为

`slaveof ip port`

ip代表主服务器的ip地址，port是redis进程的端口号。



可以使用`info replication`来查看redis服务器所处角色，服务器间关系。

不配置文件的话启动默认都是master。



### 一个例子

在同一电脑模拟三个服务端。其中一个是主服务器，另外两个是从服务器。

1. 复制三份redis配置文件，名称分别为redis6380.conf，redis6381.conf，redis6382.conf，分别代表三个服务端。

2. 修改端口号port，分别为6380 6381 6382，其中6380为主服务器端口号。

3. 再分别修改这三个配置文件的日志文件路径logfile，RDB文件名称dbfilename

    ​	如果是linux系统，还需要指定pidfile，表示当前程序的pid文件路径。

    ​	如果是linux系统，可以加上`daemonize yes`，表示后台启动。

4. 在两个从服务器的配置文件中加入`slaveof 127.0.0.1 6380`，表示这两个服务器是ip为127.0.0.1、端口号为6380Redis服务器的从服务器。

5. 启动三个服务器。



注意：因为是在一台电脑模拟三台服务器，所以需要修改logfile、dbfilename这些配置。

​			在实际中，只需要加上slaveof即可。



结果：

运行主服务器：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102152651212.png" alt="image-20210102152651212" style="zoom:47%;" />

运行两台从服务器：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102152718666.png" alt="image-20210102152718666" style="zoom: 33%;" />![image-20210102152738810](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102152738810.png)

![image-20210102152738810](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102152738810.png)



使用redis-cli.exe分别连接到三台服务器。

使用`info replication`查看服务器关系。

查看主服务器：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102152910164.png" alt="image-20210102152910164" style="zoom:50%;" />



可以看到，角色role是master，即主服务器。连接了两台从服务器，并给出了两台从服务器的ip，port和运行状态state



查看一台从服务器：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102153042651.png" alt="image-20210102153042651" style="zoom:50%;" />



可以看到，角色role是slave，即从服务器。

并给出了它的主服务器的ip，port和运行状态master-link-status：up。代表主服务器在运行，连接成功。



这时，主从服务器中数据都为空。

从服务器中不能写入数据：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102153239758.png" alt="image-20210102153239758" style="zoom:50%;" />





在主服务器中写入两个key：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102153328171.png" alt="image-20210102153328171" style="zoom:50%;" />



然后在从服务器中查看是否已经同步了主服务器的内容。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102153411104.png" alt="image-20210102153411104" style="zoom:50%;" />

可以看到，已经同步成功。



### 容灾处理

当主服务器出现故障，不能使用后。需要手动将一台从服务器转为主服务器，即从slave提升为master，然后将其他slave重新挂到新的主服务器上。

在客户端执行命令。

使用的命令有：

- slaveof no one：将对应服务器提升为master。(即我不是任何服务器的从服务器)
- slaveof ip port：在其他从服务器上使用，意思是改变该服务器对应的主服务器。

#### 例子

例如例子中的6380出现故障。

需要将6381提升为主服务器，然后6382改变为6381对应服务器的从服务器。



首先停掉6380

然后在6381对应客户端执行命令：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102154244425.png" alt="image-20210102154244425" style="zoom:67%;" />

使用`info replication`查看服务器连接信息：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102154318331.png" alt="image-20210102154318331" style="zoom:50%;" />

可以看到，已经提升为master，但是它还没有从服务器。



在6382对应客户端，执行改变主服务器命令：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102154427752.png" alt="image-20210102154427752" style="zoom:50%;" />



再次查看6381服务器信息：


<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102154457580.png" alt="image-20210102154457580" style="zoom:50%;" />



可以看到，已经有了从服务器。



如果出现故障的服务器修好了，需要再次把它加入到服务器集群中。

就应该启动它，然后把它作为现在主服务器的从服务器来使用。





### 总结

- 一个master可以有多个slave
- slave出故障，那么读请求的处理性能下降
- master出故障，那么写请求无法执行。
- 当主服务器出现故障，需要手动指定其中一台slave使用`slaveof no one`命令提升为主服务器，其他slave执行slaveof命令指向新的主服务器。
- 主从复制模式出现故障需要手动处理，如果要实现自动化处理，就要使用Sentinel哨兵，实现故障自动转移。



## Sentinel哨兵

Sentinel哨兵是redis官方提供的高可用的方案。用它来监控多个Redis服务实例的运行情况。Sentinel是一个运行在特殊模式下的Redis服务器，Sentinel哨兵是在多个Sentinel进程下互相协作的。

Sentinel系统有三个主要任务：

- 监控：Sentinel不断检查主服务器和从服务器是否按照预期正常工作。
- 提醒：被监控的Redis出现问题，Sentinel会通知管理员或其他应用程序
- 自动故障转移：当监控的主服务器不能正常工作，Sentinel会开始进行故障转移操作。将一个从服务器升级为主服务器，并让其他从服务器挂到新的主服务器上，并且为客户端提供新的主服务器地址。



每个Sentinel进程，应该独立运行在一台服务器上，多个Sentinel进程之间进行通讯，主要交换监控信息。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102160409308.png" alt="image-20210102160409308" style="zoom:50%;" />



每个Sentinel都监控主服务器，通过主服务器可以监控到从服务器。

**注意：至少要运行三个Sentinel进程，也就是至少有三台服务器运行Sentinel。并且运行的Sentinel服务器个数只能为奇数。**

**这是由Sentinel判断主服务器是否出现故障的机制决定的：每个Sentinel都独立判断主服务器是否出现故障，如果某个Sentinel进程认为主服务器出现故障，那么就投出一票。当投票数超过运行的Sentinel进程数后，那么就认定主服务器出现了故障，进行故障转移操作。也就是少数服从多数，所以至少有三台服务器运行Sentinel进程，且必须为奇数。**



**注意：Sentinel监控主服务机制：Sentinel会定期向master发送PING命令来确认master是否能够正常运行**



**注意：无需在sentinel配置文件中指定其他sentinel服务器的信息，因为sentinel可以通过发布和订阅功能来发现正在监视相同主服务器的其他Sentinel，这一功能是通过向频道sentinel:hello发送信息实现的。同样的，也无需列出主服务器属下的所有从服务器，因为Sentinel可以询问主服务器来获取这些信息。每个Sentinel会以两秒一次的频率，通过发布和订阅功能，向被它监视的所有主服务器和从服务器的sentinel:hello发送一条信息，信息中包含了Sentinel的ip地址、端口号和运行ID和完整的主服务器当前配置，当Sentinel通过所有主服务器和从服务器的sentinel:hello频道查找到之前从未出现过的sentinel时，它会将新的Sentinel添加到列表中(就是添加到自己的配置文件中)，关于这些，后面会讲到。**



**当发现主服务器出现故障并且投票数达到指定的投票数后，Sentinel会从 从服务器中选出一个来作为主服务器，并重新配置：将从服务器设置为主服务器，并将其他的从服务器挂到新的主服务器上。**



需要修改Sentinel的配置文件。

修改sentinal monitor这行，格式为：

`sentinel monitor <name> <masterIp> <masterPort> <投票数>`

其中：

- name：自定义项，表示主服务器，可以理解为给主服务器起个名
- masterIp：要监控的主服务器的ip地址
- masterPort：要监控的主服务器的端口号
- 投票数：当投票总数达到多少时，认定主服务器出现故障。例如：有三台运行Sentinel服务器，那么投票数就应该设为2。





**注意：Windows版本的Redis没有sentinel配置文件，需要手动创建，然后通过redis-server.exe来运行，并加上选项--sentinel，格式为`redis-server.exe <配置文件路径> --sentinel`**。

**如果是linux系统，那么就有redis-sentinel可执行文件和配置文件。**





### 一个例子

以上面主从复制例子为基础，在同一台电脑分别运行三个sentinel进程。监控主服务器。

创建sentinel配置文件，分别为sentinel1.conf、sentinel2.conf、sentinel3.conf

添加内容：

```conf
bind 127.0.0.1
port 6388
sentinel myid df74029ad5ecbd7677fe8a9e18a97ee086e721c2
sentinel monitor mymaster 127.0.0.1 6380 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 15000
sentinel parallel-syncs mymaster 1
```

其中端口号分别修改为6388、6389、6390。代表三个sentinel进程。

其中：

- down-after-milliseconds：该选项指定了，当sentinel向主服务器发送请求ping时，多长时间没有有效回复，被认定为主服务器故障。也就是说，发送ping命令后，如果在down-after-milliseconds毫秒内，主服务器没有作出有效回复，那么就被认定为主服务器出现故障。
- failover-timeout：表示当故障转移开始后，如果在设定时间内没有执行任务故障转移操作，当前的sentinel会认为这次故障转移失败
- parallel-syncs：当发生故障转移，主备切换时，**该选项指定了最多有多少个slave同时对新的master进行数据同步**。当同步数据时，从服务器无法处理命令请求。这个值越小，完成故障转移时间越长，但是读请求性能下降少；值越大，那么意味越多slave因为数据同步replication而不可用。

然后启动sentinel：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102170222964.png" alt="image-20210102170222964" style="zoom:67%;" />





可以看到，sentinel运行在sentinel模式下。并且会自动检测到主服务器的所有从服务器。





接下来让主服务器停掉，查看Sentinel进程有什么变化：
![image-20210102172318936](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102172318936.png)



另一个sentinel进程：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102172728948.png" alt="image-20210102172728948" style="zoom:50%;" />



可以看到，在主服务器停止工作以后，sentinel监控到了。并打印出日志。

然后从新选出了一个从服务器：端口为6381的服务器，作为主服务器。然后进行重新配置。



查看6381的信息：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102173147500.png" alt="image-20210102173147500" style="zoom:50%;" />

已经变成了主服务器。



这时，如果再重新启动6380服务器，那么该服务器会被sentinel监控到，并被设置为从服务器。

sentinel终端：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102173404158.png" alt="image-20210102173404158" style="zoom:150%;" />

另一个终端：

![image-20210102173534732](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102173534732.png)



此时再查看6381信息：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102173428555.png" alt="image-20210102173428555" style="zoom:50%;" />



已经有两个从服务器：6380和6382。



### 总结

1. Sentinel会不断检查主从服务器是否工作正常
2. 如果Sentinel出现问题，就无法监控。所以需要多个哨兵Sentinel，组成Sentinel网络。一个健康的Sentinel至少有三个Sentinel应用。彼此在不同服务器。(或者不同虚拟机)
3. 监控同一个master的Sentinel会自动连接，组成一个分布式的Sentinel网络，彼此通信并交换被监控服务器和自身信息。
4. 当一个Sentinel认为被监控的服务器下线时，它会向网络中其他Sentinel进行确认，判断服务器是否真的下线。
5. 如果下线的是主服务器，那么Sentinel网络会进行自动故障转移，通过选举从   从服务器slave中选出一个成为新的主服务器，并重新进行配置。
6. 下线的旧主服务器如果重新运行，那么Sentinel会让他成为从服务器。

**注意：Sentinel监控的不只是主服务器，它监控的是所有服务器，只是当主服务器下线时，它会进行故障转移。**



## 安全设置

### 设置密码

默认的Redis是没有密码的，这样不太安全。可以通过修改配置文件来设置连接Redis服务端时的密码。

在配置文件中，设置`requirepass` 这一行。

格式是：`requirepass 密码`



这时，使用客户端连接Redis服务端时，有两种方式：

- 在执行redis-cli时使用-a选项来设置密码，例如：`redis-cli -a 123456`
- 先执行redis-cli，但是这时不能执行任何redis命令。需要使用`auth 密码`来验证密码。

#### 一个例子

设置密码为123456，然后重新运行redis-server，并加载配置文件



使用第一种连接：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102175511144.png" alt="image-20210102175511144" style="zoom:50%;" />

同时也提示，使用-a选项在命令行直接输入密码是不安全的。

使用第二种连接：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210102175430833.png" alt="image-20210102175430833" style="zoom:50%;" />



### 修改绑定ip

在配置文件中，可以修改`bind ip`这行。

来指定可以访问redis服务器的ip地址。

多个ip使用空格分割。

### 修改默认端口

使用默认的端口比较危险。可以修改。

在配置文件中修改`port 端口号`即可。