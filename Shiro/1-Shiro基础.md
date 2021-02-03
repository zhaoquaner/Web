# 1-Shiro基础

## 简介

Apache Shiro是Java的安全框架。

Shiro可以非常容易开发出安全应用，不仅可以用在JavaSE环境，也可以用在JavaEE环境。

Shiro可以完成的功能有：

- 认证：验证用户的身份是否正确
- 授权：查看用户能做的操作，并改变用户权限
- 加密：密码存储加密
- 会话管理：与Session有关
- 与Web集成
- 缓存

等



## 架构

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210128135029808.png" alt="image-20210128135029808" style="zoom:67%;" />





- Authentication：身份认证、登录，验证用户是否拥有相应身份，例如账号密码登录。
- Authorization：授权，即权限验证。验证某个已认证的用户是否拥有某个权限。就是判断用户能做什么，不能做什么。例如：验证某个用户是否拥有某个角色，或者细粒度的验证某个用户对某个资源是否具有某个权限
- Session Manager：会话管理，用户登录后就是一次会话。在没有退出之前，所有信息都是在会话中的。
- Cryptography：加密，保护数据的安全性。如密码加密存储到数据库。
- Web Support：Web支出，容易集成到Web环境。
- Caching：缓存，例如用户登录以后，用户信息、拥有的角色、权限不用每次都去查，而是存到缓存中。提供效率
- Concurrency：Shiro支持多线程应用并发验证，即在一个线程开启另一个线程。可以把权限自动传过去。
- Testing：测试支持
- Run as：允许用户假装为另一个用户(如果允许)的身份进行访问
- Remember Me：记住我，即一次登录后，下次就不需要登录。

**注意：Shiro不回去维护用户、维护权限。这些需要我们来设计和提供。然后通过相应接口注入Shiro。**



### 外部来看



从外部来看Shiro的架构是这样的：


<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210128135744132.png" alt="image-20210128135744132" style="zoom:80%;" />



可以看到：应用直接交互的对象时Subject。即Shiro的对外API核心就是Subject。每个API的含义：

- Subject：代表当前“用户”。这个用户不一定是具体的人，与当前应用交互的任何东西都是Subject，如网络爬虫、机器人等；是一个抽象概念。所有Subject都绑定到SecurityManager，与Suject的所有交互都会委托给SecurityManager；可以把Subject当成门面，SecurityManager才是实际执行者；
- SecurityManager：安全管理器，所有和安全有关的操作都会与SecurityManager交互，且管理着所有的Subject。它是Shiro的核心。负责与后边介绍的其他组件进行交互。可看成SpringMVC的DispatherServlet前端控制器；
- Realm：域，可以当成安全数据源。Shiro从Realm获取安全数据，用户、角色、权限等，就是说：SecurityManager要验证用户身份，需要从Realm获取相应用户进行比较以确定用户身份是否合法。也需要从Realm得到相应用户进行比较来确定用户身份是否合法。也需要从Realm得到用户相应角色和权限。

按照这个架构，最简单的一个Shiro应用：

1. 代码通过Subjecy来进行认证和授权，然后Subject又委托给SecurityManager；
2. 需要给Shiro的SecurityManager注入Realm，让SecurityManager能得到合法的用户及其权限进行判断。



### 内部来看

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210128140719093.png" alt="image-20210128140719093" style="zoom: 67%;" />



从内部来看：

- Subject：主题，任何和应用交互的“用户”；
- SecurityManager：相当于SpringMVC中前端控制器。所有交互都通过SecurityManager进行控制，管理所有Subject，且负责进行认证和授权、会话和缓存管理；
- Autherticatior：认证器，负责主体认证。如果用户觉得Shiro默认的不好用，可以自定义实现。需要认证策略(Authentication Strategy)，即什么情况算用户认证通过；
- Authrizer：授权器，或者叫访问控制器，用来决定主题是否有权限进行相应的操作；
- Realm：可以有1个或多个Realm。用于获取安全数据。可以是JDBC或者内存实现等，由用户提供。因为Shiro不知道用户、权限存储在哪及以何种格式存储，所以一般在应用中都需要实现自己的Realm
- SessionManager：管理Session的生命周期。
- SessionDAO：DAO就是数据访问对象。如果想把Session保存到数据库，可以实现自己的SessionDAO，如通过JDBC、MyBatis等写到数据库，另外SessionDAO可以使用Cache进行缓存，提供性能；
- CacheManager：缓存控制器，来管理用户、角色、权限等缓存，这些数据很少改变，放到缓存提高缓存访问性能；
- Cryptography：加密模块，Shiro提供了一些常用加密组件如密码加密的算法和解密的。



## 过滤器

Shiro在Web项目中使用时，Shiro会自动创建一些默认的过滤器对客户端请求进行过滤。默认拦截器可以参考`org.apache.shiro.web.filter.mgt.DefaultFilter`中的枚举拦截器：

| 过滤器名称        | 描述                                                |
| ----------------- | --------------------------------------------------- |
| anon              | 没有参数，表示可以不需要登录就访问的请求            |
| authc             | 表示需要认证(登录)才可以访问的请求                  |
| authcBasic        | 表示需要通过httpBasic验证，如果不通过跳转到登录页面 |
| logout            | 注销登录，数据会被清除                              |
| noSessionCreation | 阻止在请求期间创建新的会话，保证无状态的体验        |
| perms             | 权限验证                                            |
| port              | 指定请求访问的端口                                  |
| rest              | 根据请求的方法                                      |
| roles             | 角色过滤器，判断当前用户是否指定角色                |
| ssl               | 表示安全的url请求，协议https                        |
| user              | 表示必须存在用户                                    |

其中anon、authcBasic、authc和user是认证过滤器。perms、roles、ssl、rest、port是授权过滤器。

使用比较多的是anthc、logout和roles。



URL模式支持Ant风格，即：

- `?`：匹配一个字符
- `*`：匹配零个或多个字符串
- `**`：匹配路径中零个或多个路径



匹配按照声明的顺序开始，使用第一次匹配优先的方式，也就是从声明顺序开始，一旦匹配成功，就停止匹配。



## 用户令牌

用户令牌，Token，实际就是身份信息，来验证用户身份的。例如最常见的用户名/密码，就是一个身份令牌。

令牌就是用户身份和证明信息的组合。

在Shiro中，`principals`代表身份，`credentials`代表证明。

- `principals`：身份，主体的标识属性。可以是任何信息，如用户名、邮箱、手机号等。唯一即可。一个主体可以有多个`principals`，但只有一个`Primary principals`。
- `credentials`：证明，凭证。即只有主体知道的安全值。如密码、数字证书等。

Shiro提供了`AuthenticationToken`接口。表示用户令牌。

该接口只有两个方法：

`getPrincipal`：获取身份信息

`getCredentials`：获取证明信息

该接口有一些常用的实现类：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210128153304287.png" alt="image-20210128153304287" style="zoom:67%;" />

最常用的就是UuserNamePasswordToken类。也就是账户名、密码。





## Realm

Realm接口有一些Shiro创建好的实现类。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210128151853660.png" alt="image-20210128151853660" style="zoom:67%;" />

常使用的是AuthenticatingRealm类和AuthorizingRealm类。分别用来认证和授权。

可以继承这些类，也可以直接实现Realm接口。这要看实现什么样的功能，来决定。

AuthenticatingRealm类中需要重写`doGetAuthenticationInfo(AuthenticationToken token)`方法。该方法用来验证用户身份。



## 使用Shiro

引用Shiro依赖。

如果使用Spring或SpringBoot依赖，那么就直接引入shiro-spring依赖。

如果不使用，那么就引入shiro-core依赖。

如果是Web开发，那么还要引入shiro-web依赖。



使用Shiro的步骤是：

1. 创建Realm类，实现Realm接口，并自定义方法

2. 创建ShiroConfig类，加上注解@Configuration，表明是配置类

3. 创建如下几个方法，全都要加上@Bean注解，这样返回的对象就会让Spring容器管理，方法的形参也会自动注入

    - `public MyRealm getRealm()`：返回一个创建的Realm对象
    - `public SecurityManager securityManager(Realm realm)`：返回安全管理器对象，上一方法创建的Realm对象会自动注入到该方法形参中。安全管理器指定Realm数据源。
    - `public ShiroFilterFactoryBean shiroFilterFactoryBean(SecurityManager securityManager)`：配置Shiro的拦截器，也就是拦截的规则。

    等其他方法。

4. 编写代码







