# Cookie



## 背景知识

会话技术：访问一个网站，只要不关闭该浏览器，不管用户点击多少超链接，访问多少资源，直到用户关闭浏览器，整个过程称为一次会话。

会话跟踪技术可以解决很多问题

- 在登录时，很多会问是否要自动登录，等下次登陆就不需要输入账户密码
- 根据我之前浏览的商品，猜我喜欢什么商品

会话跟踪技术有Cookie和Session。先说Cookie。



## 介绍

- Cookie来自于Servlet规范中一个工具类
- 如果两个Servlet来自同一个网站，并且为同一个浏览器/用户服务，那么可以使用Cookie对象进行数据共享
- cookie存放当前用户的私人数据，在共享数据过程中提高服务质量

网页之间的交互通过HTTP协议来传输数据，但是HTTP协议是无状态的协议，意思是一旦数据提交完，浏览器和服务器连接关闭，再次交互的时候重新建立新的连接，所以服务器无法确认用户信息，所有W3C组织提出：给每个用户发一个通行证，无论谁访问的时候都需要携带通行证，服务器通过这个通行证来确认用户的信息，这个通行证就是Cookie。

Cookie的工作流程：**浏览器访问数据库，如果服务器需要记录该用户的状态，就使用响应对象response向浏览器发送Cookie，可以一次添加多个Cookie，浏览器把Cookie保存起来，当浏览器再次访问服务器时，浏览器会把请求的网址连通Cookie一起交给服务器。**



响应对象将Cookie放在响应头中，浏览器请求时将Cookie放在请求头中。



## 常用方法

Cookie类用于创建一个Cookie对象，一个Cookie只能放一个键值对，且类型只能为String，**并且键值对中的value不能为中文。**

- 在response接口中定义了一个`addCookie(Cookie cookie)`方法，它用于在其响应头中增加一个响应的Set-Cookie头字段
- request接口定义了`getCookie()`方法，用于获取客户端提交的Cookie

常用的Cookie方法：

- public Cookie(String name, String value)：Cookie的构造方法，name定义变量名，value定义变量值
- setValue()和getValue方法
- setMaxAge和getMaxAge方法
- setPath和getPath方法
- setDomain和getDomain方法
- getName方法



## 使用Cookie

首先创建Cookie对象，然后使用响应对象添加Cookie，代码：

```java
response.setContentType("text/html;charset=UTF-8");
//创建Cookie，指定名字和值
Cookie cookie = new Cookie("host", “localhost")；
response.addCookie(cookie);
cookie.setMaxAge(1000);
```







## Cookie的特性

### Cookie的不可跨域名性

Cookie具有不可跨域名性，也就是说，浏览器判断一个网站能操作另一个网址的Cookie的一句是域名，例如在访问百度时，浏览器只会把百度给的Cookie发过去，但是不会带上Google的Cookie。



### Cookie保存中文

中文字符属于Unicode字符集，Cookie使用Unicode字符时需要对Unicode字符进行编码，同样，在取出Cookie时同样需要使用UTF-8进行解码。可以使用`URLDecoder.encode(String value, "UTF-8")`和`URLDecoder.decode(String value, "UTF-8")`进行编码和解码。



### Cookie的有效期

Cookie的有效期是通过setMaxAge()来设置的。

- 如果MaxAge为正数，浏览器会把Cookie写到硬盘中，只要还在MaxAge秒之前，登陆浏览器时该Cookie就有效（不论关了浏览器还是电脑）

- 如果MaxAge为负数，Cookie就是临时性的，仅仅在本浏览器内有效，关闭了浏览器Cookie就会失效，Cookie也不会写入到硬盘

- 如果MaxAge为0，则表示删除该Cookie，Cookie机制没有提供删除Cookie对应方法，所以把MaxAge设置为0等同于删除Cookie

    

**MaxAge默认值为-1。**



### Cookie的修改和删除

Cookie机制没有提供修改Cookie的方法，那么要如何修改Cookie的值呢？

先来说说Cookie的存储方式，Cookie存储类似Map集合，name=value，是一个键值对，

Cookie的名称相同，通过response添加到浏览器，会覆盖掉原来的Cookie。

所以修改Cookie时，只需要创建一个和要修改的Cookie名称相同的Cookie，value为想修改的值即可。

删除Cookie，就可以把名称相同的Cookie的MaxAge设为0，添加到浏览器，就可以实现修改。



**注意：新建的Cookie除了value、maxAge之外的其他所有属性都要和原来的Cookie相同，否则浏览器会视为不同的Cookie，不会覆盖，导致修改删除失败。**



### Cookie的域名

Cookie的domain属性决定了运行访问的域名，domain的值格式规定为".域名"

- Cookie的隐私安全机制决定Cookie是不可跨域名的，也就是说www.baidu.com和www.google.com之间的Cookie互不影响，没有关系。并且即使同一级域名，不同二级域名也不能交接，也就是说：www.google.com和www.image.google.com的Cookie也不能互相访问



如果希望一级域名相同的网页Cookie时间可以相互访问，就需要使用domain方法

例如：

```java
Cookie cookie = new Cookie("name", "zhaodaxin");
cookie.setMaxAge(1000);
cookie.setDomain(".google.com");
response.addCookie(cookie);
```

即  使用`cookie.setDomain()`来进行设置。



### Cookie的路径

Cookie的path属性决定允许访问Cookie的路径，如果值为"/"即代表所有网页的资源都可以访问Cookie，现在如果只想Servlet1可以获取到Cookie，其他资源都不可以获取，那么代码应该为：

```java
Cookie cookie = new Cookie("username", "java");
Cookie.setPath("/Servlet1");
cookie.setMaxAge(1000);
response.addCookie(cookie);
```

使用`cookie.setPath()`来进行设置。



## Cookie的安全属性

HTTP协议不仅仅是无状态的，也是不安全的。如果不希望Cookie在非安全协议中传输，可以设置Cookie的secure属性为true，那么浏览器只会在HTTPS和SSL等安全协议中进行传输。

设置secure属性为true不会将Cookie的内容加密，如果想保证安全，最好是用md5算法加密。



## 



### 显示上次用户访问时间

- 核心逻辑就是在每次登陆的时候，取到Cookie保存的上次访问时间，然后先输出上次访问时间，再更新这个值

- 访问Servlet只有两种情况:

    									1. 第一次访问
       2. 已经访问过了

    

    所以需要判断一下是第一次访问，还是已经访问过了

    代码如下：
    
    ```java
    public class OneServlet extends HttpServlet {
    
        @Override
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    
            //创建日期格式字符串
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd-HH:mm:ss");
            response.setContentType("text/html");
            response.setCharacterEncoding("UTF-8");
            PrintWriter out = response.getWriter();
    
            //获得Cookie数组
            Cookie[] cookies = request.getCookies();
    
            //初始日期值为null
            String cookieValue = null;
    
            for (int i = 0; cookies != null && i < cookies.length; i++) {
                if(cookies[i].getName().equals("time")) {
                    cookieValue = cookies[i].getValue();
                    out.print("上次访问的时间为：" + cookieValue);
                    cookies[i].setValue(sdf.format(new Date()));
                    response.addCookie(cookies[i]);
                    break;
                }
            }
    
            //如果没有日期值，那么cookieValue为null
            if(cookieValue == null) {
                out.print("这是第一次访问");
                Cookie cookie = new Cookie("time", sdf.format(new Date()));
                cookie.setMaxAge(20000);
                response.addCookie(cookie);
            }
    
    
        }
    }
    ```
    
    



#### 显示用户上次访问的商品

以书籍为例子，先定义一个Book类和一个Books类：

```java
//Book类
public class Book {
    private String id;
    private String name;
    private String author;

    public Book(String id, String name, String author) {
        this.id = id;
        this.name = name;
        this.author = author;
    }
    
    /set和get方法...
}    


//Books类
import java.util.LinkedHashMap;

public class Books {

    //使用LinkedHashMap存放书籍和对应的序号id
    private static LinkedHashMap<String, Book> books = new LinkedHashMap<>();

    static {
        books.put("1", new Book("1", "javaweb", "zhong"));
        books.put("2", new Book("2", "java", "fu"));
        books.put("3", new Book("3", "oracle", "cheng"));
        books.put("4", new Book("4", "mysql", "ou"));
        books.put("5", new Book("5", "ajax", "zi"));
    }

    //获取所有书籍信息
    public static LinkedHashMap<String, Book> getBooks() {
        return books;
    }
}
```



BooksSevlet负责输出所有书籍，并附上超链接，链接到OneServlet，点击显示书籍详细信息，并添加或者修改Cookie，Cookie名称为“bookHistory”,最多存放三本书籍id，id之间使用"_"连接，OneServlet首先显示对应书籍信息，然后添加或者修改对应Cookie，BooksServlet除了负责输出所有书籍，还需要获取浏览器发来的bookHistory对应Cookie，然后将id对应书籍显示出来。

代码为：

```java
//BooksServlet

package controller;

import entity.Book;
import entity.Books;

import javax.servlet.ServletException;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.LinkedHashMap;
import java.util.Map;
import java.util.Set;

public class BooksServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html;charset=utf-8");
        PrintWriter out = response.getWriter();

        LinkedHashMap<String, Book> books = Books.getBooks();
        Set<Map.Entry<String, Book>> entries = books.entrySet();
        out.print("<h1>网页上所有的书籍：</h1><br><ol>");
        for(Map.Entry<String, Book> bookEntry : entries) {
            Book book = bookEntry.getValue();
            out.print("<li><a href='/myweb/one?id="+ book.getId() + "' target='_blank'>" + book.getName() + "</a></li>");
        }
        out.print("</ul><hr>");

        Cookie[] cookies = request.getCookies();
        String bookHistory = null;

        for(int i = 0; cookies != null && i < cookies.length; i++) {
            if(cookies[i].getName().equals("bookHistory")) {
                bookHistory = cookies[i].getValue();
            }
        }

        out.print("<h4>您曾经浏览过的书籍：</h4>");

        if(bookHistory != null) {
            out.print("<ul>");

            String[] strings = bookHistory.split("_");
            for(int i = 0; i < strings.length; i++) {
                out.print("<li>" + books.get(strings[i]).getName() +"</li>");
            }
            out.print("</ul>");
        }


    }
}





//OneServlet

package controller;

import entity.Book;
import entity.Books;

import javax.servlet.ServletException;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;
import java.util.LinkedList;
import java.util.List;
import java.util.logging.SimpleFormatter;

public class OneServlet extends HttpServlet {


    //该方法用于获取添加或修改Cookie对应的bookHistory值
    public String makeHistory(HttpServletRequest request, String id) {

        Cookie[] cookies = request.getCookies();
        String bookHistory = null;
        for(int i = 0; cookies != null && i < cookies.length; i++) {
            if(cookies[i].getName().equals("bookHistory")) {
                bookHistory = cookies[i].getValue();
                break;
            }
        }

        //如果不存在该Cookie，那么就直接添加id
        if(bookHistory == null) {
            return id;

        } else {
            String[] strings = bookHistory.split("_");

            List list = Arrays.asList(strings);
            LinkedList<String> linkedList = new LinkedList<>();
            linkedList.addAll(list);

            //如果已经存在该id，那么删除，并重新放到第一个位置
            if(linkedList.contains(id)) {
                linkedList.remove(id);
                linkedList.addFirst(id);
            } else { //如果不存在，那么如果已经存在三个id，删除最后一个，并将该id添加到第一个
                if(linkedList.size() >= 3) {
                    linkedList.removeLast();
                    linkedList.addFirst(id);
                } else {
                    linkedList.addFirst(id);
                }
            }

            //连接字符串
            bookHistory = "";
            for(int i = 0; i < linkedList.size() - 1; i++) {
                bookHistory = bookHistory + linkedList.get(i) + "_";
            }
            bookHistory = bookHistory + linkedList.getLast();
            return bookHistory;

        }

    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html");
        response.setCharacterEncoding("UTF-8");
        PrintWriter out = response.getWriter();

        String id = request.getParameter("id");
        Book book = (Book) Books.getBooks().get(id);
        out.print("<h3>书的编号为：" + book.getId() + "</h3>");
        out.print("<h3>书的名称为：" + book.getName() + "</h3>");
        out.print("<h3>书的作者为：" + book.getAuthor() + "</h3>");

        String bookHistory = this.makeHistory(request, id);

        //如果Cookie名称相同，会覆盖先前的Cookie，所以可以直接添加新的
        Cookie cookie = new Cookie("bookHistory", bookHistory);
        cookie.setMaxAge(20000);
        response.addCookie(cookie);


    }
}

```