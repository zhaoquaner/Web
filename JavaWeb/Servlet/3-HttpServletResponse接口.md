# HttpServletResponse接口

## 介绍

- HttpServletResponse接口实现类由Http服务器提供
- HttpServletResponse接口将doGet/doPost方法执行结果写入到 响应体交给浏览器
- 由HttpServletResponse接口修饰的对象称为响应对象



## 主要功能

- 将执行结果以二进制写入到响应体
- 设置响应头中 [content-type]属性值，从而控制浏览器使用对应的编译器将响应体二进制数据编译为对应文件
- 设置响应头中[location]属性，将一个请求对象赋值给location，从而控制浏览器向指定服务器发送请求



使用响应对象来获得输出流，例如`PrintWriter out = resp.getWriter();`，然后可以使用out对象的write和print方法来进行输出，但一般都使用print方法，不使用write方法。

默认情况下，响应体的content-type属性为 "text",即文本，浏览器会将输出的内容解析成文本，如果想要输出其他内容例如HTML，那么**一定要在得到输出流之前，通过响应对象对响应体中的content-type属性改为想要输出的文本格式**。

可以使用`resp.setContentype("text/html")`和`resp.setCharacterEncoding("UTF-8");`两个方法对响应体的文件解析格式和编码方式进行设置。



### 使用响应对象的sendRedirect()方法进行地址重定向

响应对象response有一个sendRedirect方法，可以使用该方法指定浏览器对该地址进行访问，当浏览器收到响应体后，发现有location属性，会自动访问location对应的地址。同样可以使用该方法控制访问的参数。

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    	//重定向的地址,也可以加参数例如：http://www.baidu.com?userName=mike
        String result = "http://www.baidu.com";
        resp.sendRedirect(result);
        
    }
```

运行上述代码结果为：

![image-20200803140222729](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20200803140222729.png)

