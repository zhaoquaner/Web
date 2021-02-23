# Swagger

## Swagger介绍

Swagger是用来规范接口及其相关信息，并与前端和后端进行交互使用。Swagger可以生成各种格式的接口文档，以及在线接口调试页面等。使用Swagger，在开发或迭代版版本时，只需要更新Swagger描述文件，就可以自动生成接口文档和客户端服务端代码。保持调用端代码、服务端代码和接口文档的一致性。



Swagger分为几个部分：

- Swagger Codegen：通过Codegen可以将描述文件生成html和cwiki形式的接口文档。
- Swagger UI：提供了一个可视化的UI页面来展示描述文件
- Swagger Inspector：和postman差不多，可以对接口进行测试。比在Swagger UI中做接口测试返回更多信息
- Springfox Swagger：Spring基于Swagger规范，可以将基于SpringMVC和SpringBoot项目的代码，自动生成JSON格式的描述文件，本身不属于Swagger官网提供的



## 使用Swagger

使用步骤：

1. 加上pom依赖
2. 创建Swagger配置类SwaggerConfig，并加上注解@Configuration和@EnableSwagger2
3. 创建方法，加入@Bean注解，返回Docket对象
4. 运行，访问swagger-ui.html



### 加入依赖

共需要两个依赖：

- `springfox-swagger2`
- `springfox-swagger-ui`

```xml
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
```

注意版本号使用2.9.2



### 创建配置类并创建方法

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket docket() {
        ApiInfo apiInfo = new ApiInfo("学习使用的SwaggerAPI文档",
                "好好学习", "v1.0",
                "urn:tos",
                new Contact("Crayon-small-new", "", "zx1522202417@163.com"),
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList<>());
        Docket docket = new Docket(DocumentationType.SWAGGER_2).apiInfo(apiInfo);
        return docket;
    }
}
```

其中ApiInfo对象用来规定AIP文档作者信息。会显示在swagger-ui.html上。

### 运行测试

访问`http://localhost/swagger-ui.html`

![image-20210222144054270](https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210222144054270.png)



## 配置扫描接口



调用docket对象的select方法来选择需要生成文档的接口。

该方法返回一个选择器对象`ApiSelectorBuilder`，该对象主要有三个方法：

- `apis(Predicate<RequestHandler> selector)`
- `paths(Predicate<String> selector)`
- `build()`

前两个方法用来选择需要生成API接口文档的类或接口。调用`build`方法使选择生效。

前两个方法需要一个参数`Predicate`对象，选择器。

其中`apis`方法可以使用`RequestHandlerSelectors`对象，该对象有几个方法：

- `any()`：表示扫描所有类
- `none()`：表示不扫描
- `basePackage(String)`：设定扫描包路径
- `withClassAnnotation(Class<? extends Annotation>)`：表示扫描所有类上带有该注解的类。例如@Controller，传入参数Controller.class，表示扫描所有类上带有@Controoler注解的类。
- `withMethodAnnotation(Class<? extends Annotation>)`：表示扫描所有方法上都有该注解的方法。和上一个方法用法一样

例子：

```java
        docket.select().apis(RequestHandlerSelectors
                .basePackage("com.example.mybatisplus.controller"))
                .build();
```



`paths()`是用来设置扫描的路径。

使用`PathSelectors`对象来进行选择：

- `ant(String antPattern)`：设置过滤路径
- `any()`：选择所有路径
- `none()`：不选择任何路径
- `regex(String pathRegex)`：使用正则表达式进行选择

例如：

```java
        docket.select().paths(PathSelectors.ant("/hello/**")).build();
```



## 常用注解

讲一下Swagger的常用注解，需要注意，这些注解不会影响整个项目的正常使用。也就是说，即使去掉这些注解，项目也一点毛病没有。这些注解只是用来生成接口文档的。



		### @API

该注解用来修饰整个类，描述Controller的作用。

常用属性：

- `value`：String类型，URL路径
- `tags`：String数组，用来给控制器类分标签。如果设置该值，value的值会被覆盖
- `produces`：设置返回数据的类型和编码，例如`application/xml`
- `consumes`：设置处理请求的提交内容类型(Content-Type)，例如`application/xml`
- `protocols`：协议类型，可以是http、https等
- `hidden`：是否隐藏，默认false



### @ApiOperation

该注解修饰控制器类的方法

主要属性：

- `value`：URL路径
- `tags`：标签，设置该值会覆盖value值
- `notes`：注释，用来描述该接口
- `produces`：设置返回数据的类型和编码，例如`application/xml`
- `consumes`：设置处理请求的提交内容类型(Content-Type)，例如`application/xml`
- `protocols`：协议类型，可以是http、https等
- `hidden`：是否隐藏，默认false
- `response`：Class类型，返回的对象类型
- `responseContainer`：String类型，可以是List、Set和Map，其他无效
- `code`：返回状态码，默认200
- `httpMethod`：GET、HEAD、POST、PUT、DELETE、OPTIONS和PATCH



### @ApiParam

放在控制器方法的形参前

主要属性：

- `value`：对该参数简单描述
- `name`：起个别名
- `required`：该字段是否必须需要，默认false
- `type`：数据类型
- `hidden`：是否隐藏
- `defalutValue`：默认值
- `allowEmptyValue`：是否允许空值

### @ApiModel

应放在实体类上

主要属性：

- `value`：为该实体类另取一个名字，可用来描述该实体类的作用
- `description`：对该实体类的描述
- `parent`：Class类型，该实体类的父类
- `subType`：Class数组，该实体类的子类



### @ApiModelProperty

放在实体类的属性上

主要属性：

- `value`：对该字段进行简单描述
- `notes`：描述该字段
- `name`：起个别名
- `hidden`：是否隐藏
- `dataType`：数据类型
- `required`：该字段是否必须需要，默认false
- `allowEmptyValue`：是否允许空值





还有一些其他注解，不太常用，用到再去百度吧。

还有需要注意的是，在生产环境记得关闭Swagger。



