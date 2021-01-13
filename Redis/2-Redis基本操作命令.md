# 2-Redis基本操作命令



首先运行服务器端Redis：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101130538411.png" alt="image-20210101130538411" style="zoom: 50%;" />





## 查看是否连接到服务器：ping

在客户端使用ping命令，可以查看客户端是否成功连接到服务端。如果成功连接到，会返回一个PONG

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101130645009.png" alt="image-20210101130645009" style="zoom:67%;" />



## 切换库命令：select db

Redis默认创建16个库(该配置可以在配置文件中修改)，redis默认使用第一个库。

使用 `select db`来切换其他库，db代表数据库编号，从0到15。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101130851545.png" alt="image-20210101130851545" style="zoom:80%;" />



## 查看当前库中key的数目：dbsize

dbsize可以返回当前库中，key的数目。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101131011818.png" alt="image-20210101131011818" style="zoom:80%;" />





## 删除当前库数据：flushdb

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210101131115509.png" alt="image-20210101131115509" style="zoom:80%;" />



## 退出：exit或quit

注意，这个退出，只是指客户端退出，但是服务器端依然在运行。