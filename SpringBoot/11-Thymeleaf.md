# 11-Thymeleaf

## 简单介绍

Thymeleaf是一个模板引擎，采用Java开发。

Thymeleaf对网络环境要求不严格，既能用于Web环境，也能用于非Web环境。在非Web环境(即不开启服务器，只打开该文件)下，可以直接显示模板的静态数据；在Web环境下，可以像JSP一样从后台接受数据，并替换掉模板的静态数据，它是基于HTML的，以HTML为载体，Thymeleaf要寄托在HTML标签下才可以实现。

SpringBoot集成了Thymeleaf技术，SpringBoot官方也推荐使用Thymeleaf来代替JSP技术。但它本身并不属于SpringBoot。



该模板文件可以直接在任何浏览器内正确显示，浏览器会自动忽略不能理解的属性。但是这并不是一个真正有效的HTML5文档，因为HTML5文档没有`th:*`这些非标准属性的。需要Thymeleaf解析后才会变成一个标准的HTML文档。



## Thymeleaf的简单使用

首先要加入Thymeleaf的起步依赖，可以在创建SpringBoot项目时就直接添加该依赖。

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```



然后创建html文件，并且需要在文件中添加：

```
<html xmlns:th="http://www.thymeleaf.org">
```

即html文件变成：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

</body>
</html>
```



并且默认将该模板文件放在resources/templates目录中，将其他静态资源文件如js，css，图片等放在resources/static目录中。



## Thymeleaf基础语法

所有有关Thymeleaf的属性开头都是`th:*`，`*`代表任意属性。有些浏览器不支持该写法。也可以使用`data-th-*`来代替`th:*`语法。

### 显示文本

使用`th:text`和`th:utext`来显示数据。

两者不同是：

`th:text`属性不解析html元素，传来什么数据就显示什么数据。

`th:utext`属性将解析HTML元素。如传来的数据是`<p>一个段落</p>`，那么该属性会识别到<p>是一个html标签，进行解析。



### 简单表达式

| 语法    | 描述           | 作用                                                        |
| ------- | -------------- | ----------------------------------------------------------- |
| `${..}` | 变量表达式     | 取出上下文变量的值                                          |
| `*{..}` | 选择变量表达式 | 取出选择的对象的属性值                                      |
| `#{..}` | 消息表达式     | 能够从外部源(.properties文件)获得属性值，一般用于国际化消息 |
| `@{..}` | 链接表达式     | 用于表示各种超链接地址                                      |
| `~{..}` | 片段表达式     | 引用一段公共的代码片段                                      |









#### 变量表达式

语法：${变量名}

说明：标准变量表达式用于访问容器上下文环境的变量。Thymeleaf使用变量表达式来获取Controller传过来的Model中的数据。

可以是简单变量String，Integer，这时候直接使用变量名即可。

也可以是引用变量，这时候要使用${变量名.属性名}的方式。

`${..}`的作用域是面向整个上下文。



一个例子：

控制器方法：

```java
    @RequestMapping("/thymeleaf")
    public String getIndex(Model model) {
        User user = new User();
        user.setId(1);
        user.setName("张三");
        user.setAddress("河北省石家庄市");
        model.addAttribute("data", "测试Thymeleaf成功");
        model.addAttribute("user", user);
        return "index";
    }
```

html文件：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Thymeleaf</title>
</head>
<body>
    <p th:text="${data}"></p><br>
    用户的ID是:<span th:text="${user.id}"></span><br>
    用户的姓名是:<span th:text="${user.name}"></span><br>
    用户的地址是:<span th:text="${user.address}"></span><br>

</body>
</html>
```

运行结果：
<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210123193016754.png" alt="image-20210123193016754" style="zoom:50%;" />



#### 选择变量表达式

不推荐使用。

语法：*{...}

说明：也叫星号变量表达式。使用`th:object`属性来绑定对象。

然后使用`*`来代表这个对象，`*`后面的`{}`中的值时此对象的属性。

选择变量表达式`*{..}`类似于标准变量表达式`${..}`表示变量的写法。

选择变量表达式是在执行时在选择的对象上求解，而标准变量表达式是在上下文的Model上求解。

这种写法比较繁琐，不推荐使用。

选择变量表达式`*{..}`的作用域是父标签`th:object`。

例子：

控制器方法不用改，html文件改为：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Thymeleaf</title>
</head>
<body>
    <h2 th:text="*{data}"></h2>
    <div th:object="${user}">
        用户ID:<span th:text="*{id}"></span>
        用户姓名:<span th:text="*{name}"></span>
        用户地址:<span th:text="*{getAddress()}"></span>
    </div>
</body>
</html>
```

也可以不使用`th:object`绑定对象，直接使用`*{变量名.属性名}`，和`${}`用法一样。



#### 消息表达式

消息表达式`#{..}`用于国际化文字信息，也就是根据语言改变显示文字，`.properteis`文件命名规则一般是：

- `basename.properties`
- `basename_language.properties`
- `basename_language_country.properties`

其中：basename是自定义的资源文件名称，language和country是Java支持的语言和国家。如中国中文是`messages_zn_CN.properties`。

`basename.properties`是缺省加载的资源文件，当客户端根据本地语言查找不到相关资源文件，则使用该配置文件。

资源文件应该放在resources目录中。



SpringBoot加载资源文件的默认名称是messages。并且默认放在resources目录中。如果放在其他目录或者名称不是messages，那么在`application.properties`中修改`spring.messages.basename`属性来修改。如放在resources/i18n目录中，basename是message，那么属性应变为`spring.messages.basebame=i18n/message`。



##### 一个例子

创建一个资源文件文件名为`messages_zn_CH.properties`：

```properties
welcome.message=欢迎你!
```

在html文件中：

```html
    <p th:text="#{welcome.message}"></p>
```

这样就可以调用到属性值。



##### 使用占位符

消息表达式`#{..}`不允许直接处理静态文本消息，例如调用变量。但是可以在资源文件中使用占位符`{}`来处理费静态文本消息：

资源文件配置：

```properties
welcome.message={0}, 欢迎你!
encoding={0}
```

然后在html中以参数形式传递变量：

```html
    <p th:text="#{welcome.message('Jack')}"></p><br>
    字符编码是:<span th:text="#{encoding(${#request.getCharacterEncoding()})}"></span>
```

如果一个属性中有多个占位符，如`encoding={0}   {1}`，那么在参数传递时，使用逗号`,`来分割不同参数。

即：`<span th:text="#{encoding('字符编码是:',${#request.getCharacterEncoding()})}"></span>`



#### 链接表达式

语法：`@{..}`

说明：主要用于链接地址的展示，适用的标签有`<script src="">`、`<a href="">`、`<form action="">`、`<img src="">`等。可以在URL中动态获取路径，适用`+`连接字符串作为URL。



一个例子：

控制器方法

```java
    @RequestMapping("/thymeleaf")
    public String getIndex(HttpServletRequest req, Model model) {
        model.addAttribute("contextPath", req.getContextPath());
        return "index";
    }

    @RequestMapping("/hello")
    public @ResponseBody String sayHello() {
        return "Hello!";
    }
```

html文件：

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Thymeleaf</title>
</head>
<body>
    <a th:href="@{${contextPath} + '/hello'}">一个链接</a>
</body>

</html>
```

在标签a中使用`th:href`属性代替原来的href属性。并使用`@{}`来动态获取路径。



#### 片段表达式

片段表达式`~{..}`可以用来引用一段公共的HTML的代码片段。

有三种用法：

- `~{templatename}`：引用整个模板文件的代码片段
- `~{templatename::selector}`：selector可以是`th:fragment`指定的名称或其他选择器，如ID选择器，类选择器
- `~{::selector}`：相当于`~{this::selctor}`，表示引用当前模板定义的代码片段

在Thymeleaf模板文件中，使用`th:fragment`定义一段公共的代码片段：

在base.html文件中定义：

```html
<div id="footer" th:fragment="footerFragment">重复代码片段</div>
```

再在另一文件中，通过`th:insert`属性引用一段公共的代码片段：

```html
<div th:insert="~{base :: footerFragmemnt}"></div>
```

其中，`~{}`是可选的，可以去掉，变成

```html
<div th:insert="base :: footerFragmemnt"></div>
```

如果两个文件不在同一级目录，那么就要在引用文件名加上目录。



也可以使用类选择器，ID选择器来引用公共代码片段。

ID选择器语法格式：`文件名:#ID值`

类选择器语法格式：`文件名:.类值`

##### 加上参数

使用`th:fragment`属性定义代码片段时，还可以声明一组参数：

```html
<div th:fragment="frag(param1,param2)">
    <i th:text="${param1}"></i><i th:text="${param2}"></i>
</div>
```

然后在调用时需要给这两个参数赋值，

```html
<div th:insert="::frag('参数1', '参数2')"></div>
```

例如：

在base.html文件中定义代码片段：

```html
    <div th:fragment="frag(param1, param2)">
        <h3 th:text="${param1}"></h3><br>
        <h3 th:text="${param2}"></h3><br>
    </div>
```

在同级目录中引用：

```html
    <div th:insert="~{base::frag('参数1', '参数2')}"></div>
```

**注意：引用的文件路径一定要从templates下开始写，例如templates目录下有admin目录，该目录有两个文件，side.html和index.html。那么index引用admin文件，就要写`th:replace="admin/side"`才可以，视图解析器才可以解析到。**



##### 使用th:replace

**除了`th:insert`属性，`th:replace`也可以来引用公共代码段。不同的是，`th:insert`直接将代码片段插入到标签体内，而`th:replace`则是代码片段直接替换标签体内容。**

例如：在base.html文件中定义代码片段

```html
<div th:fragment="frag">这是一个代码片段</div>
```

在同级目录文件中引用：

```html
    <div th:insert="base::frag" style="color: red"></div>
    <div th:replace="base::frag" style="color: red"></div>
```

`th:insert`会把代码片段放在该属性所在的<div>标签里面，而`th:replace`则会替换该标签。

所以第一个代码片段文字是红的，第二个不是。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210125160747573.png" alt="image-20210125160747573" style="zoom:50%;" />



### 内置对象

| 对象              | 描述                           |
| ----------------- | ------------------------------ |
| `#ctx`            | 上下文对象                     |
| `#vars`           | 同#ctx，表示上下文变量         |
| `#locale`         | 上下文本地化变量，参考Locale类 |
| `#request`        | HttpServletRequest对象         |
| `#response`       | HttpServletResponse对象        |
| `#session`        | HttpSession对象                |
| `#servletContext` | ServletContext对象             |

使用方法是在变量表达式中调用内置对象：

`${内置对象}`。如`${#request.getParameter()}`





**除此之外，还有一个内置对象，param表示请求该页面时所携带的参数，不需要加符号`#`。例如：**

**URL为：`http://lcoalhost/thymeleaf?id=1`。**

**该请求返回一个index.html页面。该页面使用`th:text="${param.id}"`即可取到参数。**

**同理，还有一个session内置对象，放入session的属性应该使用该内置对象拿到。和param使用方法一样。不要加`#`。**



### 工具类

Thymeleaf提供了相关工具类来处理参数：

| 对象           | 描述                           |
| -------------- | ------------------------------ |
| `#messages`    | 消息工具类，和`#{...}`作用相同 |
| `#urls`        | 地址相关工具类                 |
| `#conversions` | 对象转换工具类                 |
| `#dates`       | 日期时间工具类                 |
| `#calendars`   | 日历工具类                     |
| `#numbers`     | 数字工具类                     |
| `#strings`     | 字符串工具类                   |
| `#objects`     | 对象工具类                     |
| `#bools`       | 布尔工具类                     |
| `#arrays`      | 数组工具类                     |
| `#lists`       | List集合工具类                 |
| `#sets`        | Set集合工具类                  |
| `#maps`        | Map集合工具类                  |



### 字面值

字面值，其实就是常量的意思。通常这些值比较简单。

#### 文字字面值

使用单引号`''`括起来的任何字符，如果字符内容里有单引号，则需要使用`\`进行转义。

#### 数字字面值

直接写数字就可以，不需要其他符。

#### 布尔字面值

直接写false或true即可。

#### 空字面值

使用null。

#### 字面令牌

字面令牌只能含有：

- 大写或小写字母，中文等不含空格和特殊符号的文本
- 0-9的数字
- 中括号
- 下划线`_`
- 连字符`-`
- 点符号`.`

字面令牌不能包含空格和特殊符号。

使用字面令牌能够对标准表达式语法进行简化。数字、布尔、空字面值其实都是字面令牌的特殊情况。

### 文本操作

可以对文本内容进行两种常用操作，字符串连接和替换。

#### 字符串连接

不管是常量还是表达式结果，都可以使用`+`连接起来。

如`<p th:text="'Welcome to ' + ${location}">`。

#### 字符串替换

符号`||`可以将字面值和表达式包括起来，这样就可以方便替换变量的值，不需要使用`+`连接符。

如`<p th:text="|Welcome to ${location}|">`



### 运算符

Thymeleaf支持大多数运算。



| 算术运算符                  | 含义                                                         |
| --------------------------- | ------------------------------------------------------------ |
| `+`                         | 加                                                           |
| `-`                         | 减                                                           |
| `*`                         | 乘                                                           |
| `/`                         | 除                                                           |
| `%`                         | 模(取余)                                                     |
| **布尔运算符**              | **含义**                                                     |
| `and`                       | 并，且运算                                                   |
| `or`                        | 或运算                                                       |
| `!`                         | 非                                                           |
| `not`                       | 非                                                           |
| **比较运算符**              | **含义**                                                     |
| `<`                         | 小于，`lt`                                                   |
| `>`                         | 大于，`gt`                                                   |
| `<=`                        | 小于等于，`le`                                               |
| `>=`                        | 大于等于，`ge`                                               |
| `==`                        | 等于，`eq`                                                   |
| `!=`                        | 不等于，`ne`                                                 |
| **条件运算符**              | **含义**                                                     |
| `(if) ? (then) : else`      | 三元运算符，`if`表达式成立，则是`then`值，否则是`else`值     |
| `(value) ?: (defaultValue)` | `value`非空(null)即真，条件为真输出`value`,否则输出`defaultValue` |

还有一种：无操作符。

当模板运行在服务器端，Thymeleaf会解析`th:*`的属性具体值来替换标签体内容，无操作符`_`则会使用原型标签体内容作为默认值。

例如：`<p th:text="${data} ?: _">数据为空</p>`，表示当data变量为空，使用原数据，即`数据为空`，否则就显示data变量值。





### 设置属性值

在Thymeleaf中，使用`th:*`(*代表任意属性)或`th:attr`来设置任意HTML标签属性的值。

不仅如此，还可以使用`th:*-*`来同时为多个不同标签属性设置相同的一个值。甚至使用`th:attrappend`和`th:attrprepend`来追加新的值到现有标签属性值中。



大部分都和HTML的属性一致，只不过加了`th:`。例如表单的action和method等属性。都可以加个`th:`。加了`th:`就可以使用Thymeleaf的表达式等内容。



#### `th:attr`

这种方式不推荐使用。了解一下就可以。

例子：使用`th:attr="href=..."`来设置标签`href`属性的值：

`<a th:attr="href=@{http://www.google.com.hk}">谷歌一下</a>`

#### `th:*`

推荐使用这种方式。显然前面哪种方式不够简洁。

`<a th:href="@{http://www.google.com.hk}">谷歌一下</a>`

这种更简单一些。

#### `th:*-*`

如果想同时为标签多个不同属性设置相同的一个值，可以使用`th:*-*`语法。

例如：

`<img src="logo.png" th:alt-title="LOGO图片">`

相当于：

``<img src="logo.png" th:alt="LOGO图片" th-title="LOGO图片">``



#### `th:attrappend & th:attrprepend`

`th:attrappend`和 `th:attrprepend`可以将表达式结果分别追加到指定属性值**之后和之前**。

如：

`<img src="logo.png" th:alt="LOGO图片" th-title="LOGO图片" th:attrappend="style='width:30px'">`



此外，还有两个常用的具体附加属性：`th:classappend="..."`和`th:styleappend="..."`。分别用来代替：

`th:attrappend="class=..."`和`th:attrappend="style=..."`。

如：`<input type="text"  th:styleappend="'color:red'" th:value="Hello">`

注意：`th:styleappend=""`的值需要使用单引号全括起来。多个style属性使用的`;`也要在单引号里。

例如：`th:styleappend="'color:red;font-size:30px'"`

#### 布尔属性

在HTML中有些是布尔属性，是指没有值的属性。如`readonly`、`checked`、`selected`等。它们存在则意味着值为true。

Thymeleaf允许使用`th:*`来选择是否使用这些布尔属性。如`th:selected`、`th:checked`和`th:readonly`。

将该属性设为true和直接写一样。



### 遍历

遍历语法是：`th:each="自定义元素变量名称 : ${集合变量名称}"`。

其中：

- 自定义元素变量名称用于表示每次迭代的元素，即集合中的一个元素
- 集合变量名称就是服务端传来的变量

并且，属性`th:each`还提供了一个用于跟踪迭代的状态变量，有以下几个属性：

- index：int类型，当前迭代的索引，从0开始
- count：int类型，当前迭代的计数，从1开始，就是索引+1。
- size：int类型，集合元素个数
- current：Object类型，就是元素类型，当前元素对象
- even：Boolean类型，当前迭代计数count是否为偶数
- odd：Boolean类型，当前迭代的计数count是否为奇数
- first：Boolean类型，当前元素是否是集合第一个元素
- last：Boolean类型，当前元素是否为集合最后一个元素

加上状态变量，则使用`th:each`的语法是：`th:each="自定义元素变量名称, 自定义状态变量名称 : ${集合变量名称}"`

例如：

```html
    <table style="font-size: 20px;border-style: solid">
        <thead>
            <td>城市</td>
        </thead>
        <tbody th:each="city, stat : ${cities}">
            <tr>
                <td th:text="|${city},索引是:${stat.index},计数是:${stat.count},现在的对					    象:${stat.current}|"></td>
            </tr>
        </tbody>
    </table>
```

结果：

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210126152746066.png" alt="image-20210126152746066" style="zoom:50%;" />



该属性的作用域是该属性所在的标签内，出了这个标签就无法使用定义的变量。

注意：**即使不定义状态变量名，Thymeleaf也会始终为每个th:each创建一个状态变量，默认状态变量名称就是自定义的元素变量名称加上Stat字符串。**



### 条件判断

条件判断语句有三种：`th:if`、`th:unless`和`th:switch`。

#### `th:if`

当表达式结果为真时显示内容，否则不显示。



当`${param != null}`时：

真假判断依据是：

- 当表达式值不为空null时：
    - 如果表达式值为Boolean类型，且值为true，则为真，否则为假
    - 如果表达式值是数字类型，且值为非0，则为真，否则为假
    - 如果表达式值是字符类型，且值为非0，则为真，否则为假
    - 如果表达式值是字符串类型，且值为非`false`、`off`、`no`，为真，否则为假
    - 如果表达式值不是布尔、数字、字符或字符串则为真
- 当表达式值为空null时，评估结果为假



#### `th:unless`

它与`th:if`正好相反，当表达式的判断结果为false时显示内容，否则不显示。

#### `th:switch`

多路选择语句，与`th:case`来使用：

```html
<div th:swtich="${role}">
    <p th:case="admin"></p>
    <p th:case="user"></p>
</div>
    
```

### 定义局部变量

使用`th:with`属性，可以定义局部变量，格式为：`th:with="变量名=变量值"`

定义多个局部变量时使用`,`分隔开。



### 内联表达式

内联表达式允许直接在HTML文本中使用标准表达式，而不需要使用`th:*`标签属性。

#### `[[...]]`

`[[]]`相当于`th:text`。不会对HTML标签转义。



#### `[()]`

相当于`th:utext`，会对HTML标签自动转义。



例子：

```java
    @RequestMapping("/thymeleaf")
    public String getIndex(Model model) {
        model.addAttribute("data", "<h1>Hello World!</h1>");
        return "index";
    }
```

html文件：

```html
    <span>[[${data}]]</span>
    <span>[(${data})]</span>
```

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210126200029833.png" alt="image-20210126200029833" style="zoom:80%;" />



#### `th:inline`

使用`[[]]`和`[()]`可以直接在HTML中使用 标准表达式，如果要使用更多功能。可以使用`th:inline`属性。它的取值有：

- none：禁止内联表达式，可以原样输出[[]]和[()]字符串
- text：文本内联，可以使用`th:each`等语法
- css：样式内联，如`<style th:inline="css">`
- javascript：脚本内联，如`<style th:inline="javascript">`



### 注释

有两种注释，一种是标准注释，注释的代码块会在文件源代码中显示出来。

一种是解析器级注释，注释的代码块会在引擎解析的时候去掉。

#### 标准注释

语法：`<!--内容--->`。

#### 解析器级注释

语法：`<!--/*内容*/-->`。

