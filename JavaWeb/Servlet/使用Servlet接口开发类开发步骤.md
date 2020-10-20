# 使用Servlet接口开发类开发

一共有三步：

1. 创建一个类继承HttpServlet实现类，使之成为Servlet接口实现类

2. 重写HttpServlet父类两个方法，doGet或者doPost

    ​	浏览器使用get方式---->Servlet.doGet()

    ​	浏览器使用post方式--->Servlet.doPost()

3. 将Servlet接口实现类信息**注册**到Tomcat服务器

    ​	在web文件夹中找到web.xml文件，在该文件里注册实现类信息

    ```xml
    <servlet>
    	<servlet-name></servlet-name><!--声明一个变量名称存储servlet实现类类路径-->
        <servlet-class></servlet-class><!--声明servlet接口路径-->
    </servlet>
    ```

    然后tomcat服务器会使用name和class路径来存储Servlet实现类路径

    

    用户在访问实现类时需要写完整的类路径，这样太麻烦，为了降低用户访问Servlet接口实现类难度，需要设置简短的请求别名

    ```xml
    <servlet-mapping>
        <servlet-name></servlet-name><!--这是在上面servlet标签中，和servlet-name一样-->
        <url-pattern></url-pattern><!--设置简短的请求别名，在书写时必须以"/"开头-->
    </servlet-mapping>
    ```

    

## Servlet对象生命周期

1. 网站中所有Servlet实现类实例对象，只能由Http服务器负责创建，开发人员不能手动创建

2. 在默认情况下，Http服务器接收到对于当前Servlet接口实现类第一次请求时自动创建这个实现类的实例对象，如果没有请求，那么则不会创建该实例对象

    

    在手动配置下，可以要求Http服务器在启动时自动创建某个实现类实例对象

    ```xml
    <servlet>
    	<servlet-name></servlet-name><!--声明一个变量名称存储servlet实现类类路径-->
        <servlet-class></servlet-class><!--声明servlet接口路径-->
        <load-on-startup> </load-on-startup><!--填写一个大于0的整数，默认值为0-->
    </servlet>
    ```

    ​	使用load-on-startup标签来设置当服务器启动时创建实例对象

    

3. 在Http服务器运行过程中，一个Servlet接口实现类只能创建出一个实例对象，如果有多个请求访问该实例对象，也只会创建一个，多个请求访问这一个，即单例多线程

4. 在Http服务器关闭时，所有的Servlet对象都会被销毁

