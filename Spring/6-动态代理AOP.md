# 6-动态代理AOP



## 简介

​		AOP(Aspect Orient Programming)，面向切面编程，是一种可以通过运行期间动态代理实现程序功能的统一维护的一种技术。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重写性，同时提高了开发效率。



​		具体来说，面向切面编程就是将交叉业务逻辑封装成切面，利用AOP容器的功能将切面植入到主业务逻辑中，交叉业务逻辑是指，通用的、与主业务逻辑无关的代码，例如日志，缓存，安全检查等。



​		AOP底层，采用的就是动态代理模式，采用了两种代理：jdk的动态代理，和CGLIB的动态代理。

​	    AOP实际就是动态代理的规范化，把动态代理的实现步骤、方式定义好，开发人员使用统一的方式实现动态代理。

### jdk动态代理

​		使用jdk的Proxy、Method和InvocationHandler创建代理对象，**jdk动态代理要求目标类必须实现接口。**

### CGLIB动态代理

​		是第三方的工具库，创建代理对象，原理是继承。**通过继承目标类，来创建子类，子类就是代理对象。**这种方式要求目标类不能是final，方法也不能是final。



## 切面

### 一些术语

1. **Aspect：切面，表示增强的功能。一般是一个类，也叫作切面类。完成一个非业务功能。常见有日志，事务，权限验证。**
2. JoinPoint：连接点，连接业务方法和切面的位置，就是目标类的业务方法。
3. **Pointcut：切入点，指多个连接点方法的集合，多个方法。**
    - **执行目标对象方法，动态的植入切面代码**
    - **通过切入点表达式，指定拦截哪些类的哪些方法，给指定的类在运行的时候植入切面类代码**
4. **切入点表达式：指定哪些类的哪些方法被拦截，即被植入切面类代码**
5. 目标对象：给哪个类增加功能，那么这个类就是目标对象
6. Advice：通知，通知表示切面功能指向的时间，在目标方法之前还是之后。

### 切面的三个关键要素

1. 切面的功能代码，也就是切面能干什么
2. 切面的执行位置，使用Pointcut来表示，也就是切面代码在哪个位置
3. 切面的执行时间，使用Advice表示时间，在目标方法之前还是之后。



## AOP的实现

AOP是动态代理的一个规范化。

AOP的技术实现框架：

1. spring：spring在内部实现了AOP规范，Spring主要在事务处理中使用AOP，在项目开发中很少使用Spring的AOP实现，因为比较笨重。
2. AspectJ：一个开源的框架，专门做AOP。Spring框架中集成了aspectj框架，通过spring就可以使用aspectj的功能。



aspectj框架实现AOP有两种方式：

- 使用XML的配置文件
- 使用注解，常用。





## AspectJ

### AspectJ中的几个常用类



#### JoinPoint接口

JoinPoint是Aspectj框架的一个类，表示切入点，它包含被执行的方法的一些信息，例如参数，方法修饰符等等。

在使用通知注解拦截目标方法后，可以在被注解方法签名中加上该参数，来获取被拦截的方法的一些信息。

常用方法：

- `Signature getSignature()`：获取封装了署名信息的对象，该对象可以获取到目标方法名，所属类的Class等信息
- `Object[] getArgs()`：获取传入目标方法的参数对象
- `Object getTarget()`：获取被代理的对象
- `Object getThis()`：获取代理对象

#### ProceedingJoinPoint接口

该接口是JoinPoint的子接口，只在环绕通知后的方法参数中使用。

除了上述方法，还有一个proceed的方法，用来执行目标方法。



### aspectj的切入点表达式

AspectJ定义了专门的表达式用于指定切入点，表达式的原型是：

​			`execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern)  throws-pattern?)`

​	解释：

- modifiers-pattern：访问权限类型
- **ret-type-pattern：返回值类型**
- declaring-type-pattern：包名类名
- **name-pattern(param-pattern)：方法名(参数类型和参数个数)**
- thorws-pattern：抛出异常类型



​	？代表可选的部分

​		加粗代表必须部分

用中文来表达就是：

`execution(访问权限  方法返回值   方法声明(参数) 异常类型)`

各部分使用空格分开，在其中可以使用以下符号

- *：0至多个任意字符
- .. ：用在方法参数中，表示任意多个参数；用在包名后，表示当前包及其子包路径
- +：用在类名后，表示当前类及其子类；用在接口后，表示当前接口及其实现类

例子：

- execution(public * *(..))：指定切入点为：任意的公共方法
- execution(* set*(..))：指定切入点为：任意一个以“set”开始的方法
- execution(* com.exy.service. * . *(..))：指定切入点为com.exy.service包中任意一个类的任意一个方法



### AspectJ通知注解

所有通知的方法都包含一个JoinPoint类型参数，就是一个切入点表达式。

#### @Before

​		在目标方法执行之前执行。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143540935.png" alt="image-20201129143540935" style="zoom:50%;" />



#### @AfterReturning

在目标方法执行之后执行，所以可以获取到目标方法的返回值。该注解的returning属性就是用于指定接收方法返回值的变量名的。所以被注解为后置通知的方法，除了可以包含JoinPoint参数(**该参数必须放在被注解方法的第一个参数位置**)外，还可以包含用于接收返回值的变量，该变量最好为Object类型。



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143712141.png" alt="image-20201129143712141" style="zoom:50%;" />



#### @Around

在目标方法之前或之后执行。**被注解为环绕增强方法要有返回值，推荐使用Object类型，**并且方法可以包含一个ProceedingJoinPoint类型的参数，接口ProceedingJoinPoint有一个proceed方法，用于执行目标方法，若目标方法有返回值，那么该方法的返回值就是目标方法返回值，最后环绕增强方法将返回值返回。该增强方法实际是拦截了目标方法的执行。



环绕通知实际就等同于是jdk的动态代理。



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143222572.png" alt="image-20201129143222572" style="zoom:43%;" />



因为目标方法是在被注解方法中，调用ProceedingJoinPoint的proceed方法执行的，所以被注解方法可以影响目标方法的执行，例如只在参数等于某个值或不等于某个值时执行。

例子：

doAround方法

```java
    @Override
    public String doAround(String name) {
        System.out.println("执行doAround方法,name为:" + name);
        return name;
    }
```



切面类中的方法

```java
	@Around(value = "execution(* doAround(..))")
    public Object myAround(ProceedingJoinPoint pjp) throws Throwable {
        Object[] args = pjp.getArgs();
        //判断传入参数是否为空，并且长度是否大于零
        if(args != null && args.length > 0) {
            String name = (String) args[0];
            //如果等于Jack，那么就执行目标方法，否则额拦截该方法
            if(name.equals("Jack")) {
                pjp.proceed();
            } else {
                System.out.println("方法被拦截，doAround方法没有执行!");
            }
        }
        return null;
    }
```

执行结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201201094811143.png" alt="image-20201201094811143" style="zoom:33%;" />![image-20201201094837260](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201201094837260.png)



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201201094857381.png" alt="image-20201201094857381" style="zoom:33%;" />





#### @AfterThrowing

在目标方法抛出异常后执行，该注解的throwing属性用于指定所发生的的异常类对象，被注解为异常通知的方法可以包含JoinPoint参数和Throwable参数，参数名称为thorwing指定的名称，表示发生异常的对象。



<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143432571.png" alt="image-20201129143432571" style="zoom:53%;" />

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143504626.png" alt="image-20201129143504626" style="zoom:50%;" />



#### @After

无论方法是否抛出异常，该增强方法都会执行。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129143808737.png" alt="image-20201129143808737" style="zoom:50%;" />





### Pointcut

​		当较多的通知增强方法使用相同的execution切入点表达式时，编写、维护比较麻烦。Aspectj提供了@Pointcut注解，来定义execution切入点表达式。

​		用法是，将@Pointcut注解在一个方法之上，**以后所有的execution的value属性值都可以使用该方法名作为切入点**，代表的就是@Pointcut定义的切入点，使用@Pointcut注解的方法一般使用private标识，没有实际作用。

​		实际上使用@Pointcut，就相当于定义了一个变量，可以重复使用。



例如：

```java
@Pointcut(value = "execution(* *..service.*.*(..))")
private void mypointcut(){}
```

那么在之后使用切入点表达式就可以直接写`@After(value = "mypointcut()")`即可。



## 使⽤Spring AOP开发步骤(Aspectj)

### 引入AOP相关jar文件

- spring-aop--3.2.5.RELEASE.jar
- aopalliance.jar
- aspectjweaver.jar
- aspectjrt.jar

其实就是引入spring依赖和aspectj依赖。



### 注解⽅式实现AOP编程

我们之前⼿动的实现AOP编程是需要⾃⼰来编写代理⼯⼚的，现在有了Spring，就不需要我们⾃⼰写代 理⼯⼚了。Spring内部会帮我们创建代理⼯⼚。也就是说，不⽤我们⾃⼰写代理对象了。 因此，我们只要关⼼**切⾯类、切⼊点、编写切⼊表达式指定拦截什么⽅法就可以了！**



#### 创建切面类

​		其实就是普通类，需要在类上面加入`@Aspect`注解，来表明它是个切面类。

​		在类中定义方法，方法就是切面要执行的功能代码，在方法的上面加上aspectj的通知注解，例如`@Before`或者`@After`，并且需要指定切入点表达式execution()

​		其中在切面类中定义方法，该方法有限制：

- 必须是公共方法，public

- 方法没有返回值，@Around除外

- 方法名称自定义

- 方法可以有参数，也可以没有

     如果有，那么参数不是自定义的，有几个参数类型可以使用

    上面已经写了通知注解，除了切入点表达式参数。

    如果有其他参数，那么被注解的方法也要有相应参数，

    例如注解`@AfterReturning(value = "execution(* *(..))", returning="result")`，有一个returning参数，参数名为result，代表目标方法执行后的返回值，那么在被注解的方法签名中也要有参数`Object result`，参数名必须相同。

    其他例如异常通知也是这样。

    被注解方法本身还可以有JoinPoint参数。

    被注解的方法的参数不需要主动赋值，是aspectj框架自动赋值的。

#### 创建spirng配置文件

​		在配置文件中声明对象，把对象统一交给容器管理。

​		声明对象可以用注解，也可以用XML配置。



​		并且声明aspectj框架中的自动代理生成器标签`<aop:aspectj-autoproxy>`，这个标签是用来完成代理对象的自动创建功能的。

​		工作原理是，`<aop:aspectj-autoproxy>`通过扫描找到@Aspect定义的切面类，再由切面类根据切入点找到目标类的目标方法，再由通知类型找到切入的时间点。



**注意：如果找不到对应的目标方法，那么就不会把代理代码植入到目标类的目标方法中，执行的就是原来代码。**



#### 使用ApplicationContext获取目标对象

这里的目标对象就是经过aspectj修改后的代理对象，也就是目标类对象。







###  一个例子

​		使用注解完成容器管理对象。



先创建接口有一个doSome方法，实现这个接口创建一个目标类，实现doSome方法，然后再创建切面类，实现在执行doSome方法之前，输出当前时间的功能。



创建接口：

```java
public interface SomeService {
    void doSome(String name, Integer age);
}
```

创建接口实现类：

```java
@Component("someService")
public class SomeServiceImpl implements SomeService {
    @Override
    public void doSome(String name, Integer age) {
        System.out.println("名字为 :" +name + ", 年龄为：" + age);
    }
}
```

创建切面类：

```java
@Aspect
@Component("myAspect")
public class MyAspect {

    @Before("execution(public void doSome(String, Integer))")
    public void myBefore() {
        System.out.println("切面功能，输出执行时间：" + new Date());
    }
}
```



测试代码：

```java
    @Test
    public void Test01() {
        String config = "applicationContext.xml";
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(config);
        SomeService proxy = (SomeService) applicationContext.getBean("someService");
        proxy.doSome("Jack", 21);
    }
```

运行结果

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20201129145426769.png" alt="image-20201129145426769" style="zoom:50%;" />





### 注意

如果目标类有接口，那么Spring框架默认会使用jdk的动态代理，如果没有接口，那么spring会使用CGLIB代理。

但是如果希望在有接口的情况下，让Spring使用CGLIB动态代理，就需要在自动代理生成器标签中添加一个属性，`proxy-target-class = "true"`



即`<aop:aspectj-autoproxy  proxy-target-class="true" />`

就可以让Spring默认使用CGLIB代理。













