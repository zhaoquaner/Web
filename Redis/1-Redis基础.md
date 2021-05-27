# 1-Redis基础

## Redis介绍



​		Redis是一个key-value存储系统，数据缓存在内存中，同时Redis会周期性的把更新的数据写入磁盘或修改操作写入追加的记录文件。

​		Redis支持主从同步，数据可以从主服务器上向任意数量的从服务器上同步。

​		Redis的value值支持5种数据类型，分别是字符串String，列表List、集合Set、有序集合zSet和哈希Hash。



## 为啥使用Redis

​		Redis是非关系型数据库，即NoSQL。在NoSQL数据库中数据之间是无联系的，没有关系，数据的结构是松散、可变的。

非关系型数据库的优势：

- 大数据量，高性能

    NoSQL数据库都具有非常高的读写性能，尤其是在大数据量下，表现很优秀。这得益于数据的无关系型，数据库结构简单。

- 灵活的数据模型

    NoSQL无需事先为要存储的数据建立字段，随时可以存储自定义的数据格式。

- 高可用

- 低成本



当然，非关系型数据库也有劣势：

- 数据之间没有关系
- 不支持标准的SQL，没有公认的NoSQL标准
- 没有关系型数据库的约束，大多数也没有索引概念
- 没有事务，不能依靠事务实现ACID
- 没有丰富的数据类型，如日期，数值，二进制等



## Redis简单使用

Window版本的Redis不是真正的Redis，写出Reids数据库的这个人，是基于Linux系统，只写了Linux版本。Windows版本的Redis是微软公司推出的。但是和Linux版本的Redis使用基本相同。

在开发环境中，可以使用Windows，但在实际生产环境中，要使用Linux版本的。

### Windows下使用

Redis有三个文件比较重要，redis-cli.exe、redis-server.exe和redis.window.conf，分别对应redis客户端，redis服务器端，redis的Windows版本配置文件。

在使用时，要先运行服务器端，然后运行客户端。

如果修改了配置文件，那么运行服务器时，要在./redis-server.exe 后加上 配置文件路径 redis.window.conf，让redis重新加载该配置文件。

Windows版本的Redis不支持后台启动。



**不要将Redis添加到Windows服务，会出错，很痛苦。**

### Linux下使用

Linux和Windows文件一样。

Linux启动redis有两种方式：

- 前台启动    ./redis-server：启动后，该终端不能输入其他命令，也不能关闭，只能运行redis-sever
- 后台启动   ./redis-server &：启动后，终端可以继续输入其他命令

一般使用后台启动。

Linux关闭redis服务也有两种方式：

- 在redis客户端使用shutdown命令关闭。这种关闭方式，redis会先执行完数据操作，再关闭服务器
- 直接使用kill命令杀死进程。不推荐这种方式，有可能会造成数据丢失





## Redis客户端

Redis客户端是一个程序，通过网络连接到Redis服务器，在客户端软件中使用Redis可以识别的命令，向Redis服务器发送命令。

就是和Redis服务器进行交互的工具。



redis-cli.exe是Redis自带的命令行客户端。

有两种连接reids服务器的方式

- 直接运行redis-cli.exe，这时，默认使用ip 127.0.0.1 端口6379
- 运行redis-cli.exe，使用选项连接服务器，-h 主机   -p  端口，例如./redis-cli -h 127.0.0.1 -p 6379

连接远程Redis服务器，还需要修改服务器端配置文件，因为Redis有安全保护措施，默认只有本机才可以访问。

修改两个地方：

- bind ip ：用来绑定ip，即只有该ip才可以访问Redis。注释掉该行。
- protected-mode yes：设置保护模式，改为no

需要重新加载redis配置文件。





## Redis日志

在redis的配置文件中可以配置Redis的日志级别和日志输出路径

### loglevel

loglevel配置项用来配置输出日志级别，有四个级别：

- debug：会打印很多信息，适用于开发和测试阶段
- verbose：比debug清爽一点
- notice：适用于生产环境，**默认值。**
- warning：警告信息

开发时，可以使用debug，在生产环境使用notice

### logfile

logfile用来指定日志输出路径。

使用双引号来指定路径，例如`logfile "D:/logs/redis.log"`

如果注释掉该行，那么默认输出到控制台。















