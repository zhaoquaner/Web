# 使用HttpServletRequest进行数据共享



## 介绍

request对象也是一个域对象，即在一个范围内，可以进行共享。

在一次请求中，利用请求转发进行多个Servlet调用时，使用的是同一个request对象，这时候，就可以利用该对象进行数据共享。

## HttpServletRequest相关API

有两个最主要方法进行数据共享

- void setAttribute(String s, Object o)：指定名字和任意数据类型的数据存入到request中
- Object getAttribute(String s)：获取名为s的数据

在request内部维护着一个Map集合，可以存放数据。









这个简单，没有例子！Yeah～

