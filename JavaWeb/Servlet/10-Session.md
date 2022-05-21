# Session



## 什么是Session

Session是另外一种记录浏览器状态的机制，不同的是Cookie保存在浏览器中，Session保存在服务器中。用户使用浏览器访问服务器时，服务器会把用户的信息以某种形式记录在服务器中。

如果把Cookie比做 检查用户带来的通行证来确认用户身份，那么Session就是通过检查服务器上的“客户信息表”来确认用户身份信息，Session相当于在服务器中建立了一份“客户明细表”。





## 为什么使用Session技术

Session比Cookie使用方便，Session可以解决Cookie解决不了的事，适用范围更广，例如Session可以存储对象，但是Cookie只能存储字符串。



## Seesion API

- long getCreationTime()：获取Seesion被创建的时间
- **String getId()：获取Session的id**
- long getLastAccessedTime()：返回Session最后活跃的时间
- ServletContext getServletContext()：获取ServletContext对象
- **void setMaxInactiveInterval(int var1)：设置Session超时时间**
- **int getMaxInactiveInterval()：获取Session超时时间**
- **Object getAttribute(String var1)：获取Session属性**
- Eumeration getAttributeName()：获取Session所有的属性名
- **void setAttribute(String var1, Object var2)：设置Session属性**
- **void removeAttribute(String var1)：移除Session属性**
- **void invalidate()：销毁该Session**
- boolean isNew()：该Session是否为新的



## HttpSession接口

### 介绍

- HttpSession接口来自于Servlet规范下的一个接口，实现类由Http服务器提供
- 如果两个Servlet来自于同一个网站，并且为同一个浏览器/用户提供服务，那么此时可以通过HttpSession对象进行数据共享
- 习惯上将HttpSession接口修饰对象称为ie会话作用域对象



可以看出，Session有着和request和ServletContext类似的方法，Session作为一种记录浏览器状态的机制，只要Session没有被销毁，Servlet之间就可以直接通过Session对象实现通讯。



简单例子：

```java
//Servlet1代码

//得到Session对象
HttpSession session = request.getSession();
//设置Session属性
session.setAttribute("name", "哈哈哈哈哈哈");

//Servlet2代码

//获取从Servlet1的Session存进去的值
HttpSession session = request.getSession();
String value = (String) session.getAttribute("name");
System.out.println(value);

```



一般来讲，当我们要存进去的是用户级别的数据就用Session，用户级别就是：只要浏览器不关闭，希望数据还在，就是用Seesion保存。





## Session的实现原理

**当浏览器访问某个网页时，会在服务器端内存开辟一块内存，这块内存就叫做Session，，这个内存是和浏览器关联在一起的，这个浏览器指的是浏览器窗口，或者是浏览器的子窗口，意思是：只允许当前这个session对应的浏览器访问，就算再在同一个机器上启动新的一个浏览器也是无法访问的，而另外一个浏览器也需要记录Session的话，服务器就会再创建一个属于这个浏览器的session。**

**简单来说，一个客户端的一个处于打开状态的浏览器对应一个Session。**

**需要注意的是，例如，谷歌浏览器访问一个网站，在不关闭前一个浏览器的情况下，再打开一个谷歌浏览器窗口打开同一个网站，访问的是一个Session。但是如果关闭了前一个浏览器，再打开该浏览器打开该网站，那么就会创建新的Session。**



但是，如果按照上述代码，在浏览器新建一个会话，先访问Servlet2，那么会出现空指针异常。

那么问题来了：服务器是如何实现一个session为一个用户浏览器服务的呢？换个说法：为什么服务器能够为不同用户浏览器提供不同session？



**Http协议是无状态的，Session不能依据HTTP连接来判断是否为同一个用户，于是：服务器向用户发送了一个名为JESSIONID的Cookie，它的值是Session的id值，Session依据这个Cooie来识别是否为同一个用户。**



**简单来说，Session之所以可以识别不同用户，依靠的就是Cookie。该Cookie是服务器自动颁发给浏览器的，不需要手动创建，该Cookie的maxAge默认值为-1，也就是说仅供当前浏览器使用，不会将该Cookie存在硬盘中。**



捋一下思路流程：当访问Servlet1时，服务器会创建一个Session对象，执行程序代码，并且自动颁发给Cookie给用户浏览器。



当使用同一个浏览器访问Servlet2时，浏览器会把Cookie发给服务器，服务器通过Cookie存储的Session的ID就知道该浏览器使用的是哪个Session。



但是当使用新会话浏览器直接访问Servlet2时，该新浏览器没有Cookie，所有服务器无法辨认使用哪个浏览器，所以就获取不到值，所以前面例子出现空指针异常。



### URL地址重写

前面提到，Session是通过Cookie来识别浏览器，但是如果用户浏览器禁用了Cookie（**注意，禁用Cookie意味着浏览器不能向服务器发送Cookie，但是浏览器还是可以接受Cookie，也就是响应头可以有Cookie，但是请求头不能有**），那么应该怎么办，JavaWeb提供了另一种方式：URL地址重写

HttpServletResponse类提供了两个URL地址重写的方法：

- encodingURL(String url)
- encodingRedirectURL(String url)

这两个方法会自动判断该浏览器是否支持Cookie，如果支持Cookie，重写后的URL地址就不会带有jsessionid了（当然，即使浏览器支持Cookie，第一次输出URL地址时还是会出现jsessionid，因为没有任何Cookie可带）。

使用Session，改写一下Cookie文档商品例子里的代码：

```java
//之前的代码，超链接直接为对应的链接

        for(Map.Entry<String, Book> bookEntry : entries) {
            Book book = bookEntry.getValue();
            out.print("<li><a href='/myweb/one?id="+ book.getId() + "' target='_blank'>" + book.getName() + "</a></li>");
        }

//修改后的代码,将链接使用encodingURL进行编码，然后再进行链接

        for(Map.Entry<String, Book> bookEntry : entries) {
            Book book = bookEntry.getValue();
            String url = response.encodeURL("/myweb/one?id=" + book.getId());
            out.print("<li><a href='"+ url + "' target='_self'>" + book.getName() + "</a></li>");
        }

```



URL地址重写的原理为：将Session的id重写到URL地址中，服务器通过解析后重写URL，获取Session的id，这样一来，即使浏览器禁用了Cookie，但是Session的id还是可以通过服务器端传递。

## Session的生命周期和有效期

Session在用户第一次访问服务端Servlet，jsp等动态资源时就会自动创建，Session对象保存在内存中。

Session生成后，只要用户继续访问，服务器就会更新Session的最后访问时间，无论是否对Session进行读写，服务器都会认为Session活跃了一次。

由于随着越来越多用户访问服务器，因此Session也越来越多，为了防止内存溢出，服务器会把长时间没有活跃的Session从内存中删除，这个时间就是Session的超时时间。

Session默认超时时间为30分钟，有三种方式可以对Session的超时时间进行修改：

1. 在tomcat/conf/web.xml文件中设置，对所有的WEB应用都有效

    ```xml
    <session-config>
        <session-timeout>20</session-timeout><!--修改超时时间为20分钟-->
    </session-config>
    ```

2. 在单个web.xml文件中设置，只对单个web应用有效，如果冲突，以自己的web与你管用为准，代码和第一种一样。

3. 通过setMaxInactiveInterval()方式设置

    ```java
    //设置最长超时时间为60秒，这个方法参数为秒
    session.setMaxInactiveInterval(60);
    ```





## Session的简单应用



### 使用Session完成用户简单登录

在不关闭浏览器的情况下可以自动登录，而不用再次输入密码

思路：首先写一个静态HTML文件Login.html，用作登录界面，输入帐户密码。有两个Servlet实现类LoginServlet和IndexServlet，LoginServlet用来验证输入帐户密码是否匹配，输出对应提示信息，IndexServlet用来检查Session是否已经有了User，如果有了证明之前登录成功，直接提示登录成功信息，如果没有，重定向到登录界面。并将IndexServlet设置为默认打开文件。

代码（User类和UserDB类省略）：

```java
//LoginServlet的doPost方法
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html;charset=utf-8");

        //获得传来的参数
        String username = request.getParameter("username");
        String password = request.getParameter("password");

        //验证是否为正确的用户名和密码
        User user = UserDB.find(username, password);

        if(user == null) { //如果不是，输出登录失败提示信息
            response.getWriter().print("<h3>用户名或密码错误，无法登陆!</h3>");
            return;
        }

        //添加该user对象到Session中
        HttpSession session = request.getSession();
        session.setAttribute("user", user);
		//打印登录成功提示信息
        response.getWriter().print("成功登录!");
        
    }

//IndexServlet的doGet方法

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html;charset=utf-8");

        //首先获得User对象，如果为空，证明没有登录成功过，转到登录界面，否则打印登录成功提示信息
        HttpSession session = request.getSession();
        User user = (User) session.getAttribute("user");

        if(user == null) {
            response.sendRedirect("Login.html");
        } else {
            response.getWriter().print("用户登录成功！");
        }

    }
```







## Session和 Cookie的区别

1. 从存储方式上比较

    - Cookie只能存储字符串，如果要存储非ASCII字符串还要进行编码
    - Session可以存储任意类型的数据，相当于一个容器

2. 从隐私安全比较

    - Cookie存储在浏览器，对客户端可见，信息容易泄漏出去，如果使用，最好进行加密
    - Session存储在服务器上，对客户端透明，不存在隐私泄漏问题

3. 从有效期上比较

    - Cookie可以保存在硬盘上，只需要设置maxAge为比较大的正整数
    - Session保存在服务器中，设置maxInactiveInterval属性值来确定Session有效期，并且Session依赖于JSESSIONID的Cookie，该Cookie默认maxAge属性为-1，如果关闭了浏览器，该Session虽然没有从服务器中消亡，但也无法再使用，失效了

4. 从对服务器的负担比较

    - Session是保存在服务器的，每个用户都会产生一个Session，如果并发访问量很大，是不能使用Session的，Session会占用大量内存
    - Cookie是保存在客户端的，不占用服务器资源，像一些大型网站，一般都会使用Cookie来进行会话跟踪

5. 从浏览器支持上比较

    - 如果浏览器禁用Cookie，Cookie就无法使用了
    - 禁用Cookie，Session还可以通过URL地址重写来进行会话跟踪

6. 从跨域名上比较

    - Cookie可以设置domain属性来实现跨域名
    - Session只在当前域名内有效，不可跨域名

    





如果仅仅使用Cookie或者Session达不到理想效果，那么可以两者结合来使用。

例如，做一个购物系统，使用Session来记录购买过的商品，当关闭浏览器时，购买记录就全部消失，这时候可以使用Cookie来保存购买记录。

总之，两者要灵活使用，以达到我们希望的效果。









