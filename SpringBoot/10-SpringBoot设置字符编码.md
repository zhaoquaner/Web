# 10-SpringBoot设置字符编码

配置字符编码有两种方式，一种是在application.properties中设置，另一种就是用传统的字符编码过滤器。

推荐使用第一种方式，这里也只介绍第一种方式。

非常简单，在application.properties设置三个属性即可。

```properties
#设置请求响应的字符编码
server.servlet.encoding.enabled=true
server.servlet.encoding.force=true
server.servlet.encoding.charset=UTF-8
```

第一个属性作用是启用编码。

第二个属性作用是强制使用设置的编码。

第三个属性指定要使用的字符集。

设置这三个属性就可以了。



在之前版本使用的是`server.http...`，这个属性被弃用了，使用现在这个`server.servlet`.就可以。



