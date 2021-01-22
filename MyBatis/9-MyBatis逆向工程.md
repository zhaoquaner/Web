# 9-MyBatis逆向工程

MyBatis逆向工程，简单说，就是通过数据库表来自动生成映射文件、DAO接口和实体类。而不需要手动去写。

使用方法：

1. 创建GeneratorConfig.xml配置文件
2. 在pom文件中加入mybatis-generator-core依赖，和mybatis-generator的maven插件
3. 编写配置文件
4. 使用插件来生成实体类、mapper文件和Dao接口

也可以创建MyBatis-Generator类来代替maven插件生成对应类，但比使用插件麻烦很多。



## 配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    
    
    
    <!--指定连接数据库的JDBC 驱动包所在位置，指定到你本机的完整路径-->
    <classPathEntry location="D:\software\Others\Maven\maven-repository\mysql\mysql-connector-java\8.0.22\mysql-connector-java-8.0.22.jar"/>
    
    
    
    
    <!--配置table表信息内容体，targetRuntime 指定采用MyBatis3的版本-->
    <context id="tables" targetRuntime="MyBatis3">
        
        
        
        
        
        <!--抑制生成注释，由于生成的注释都是英文的，可以不让它生成,true为不生成注释-->
        <commentGenerator>
            <property name="suppressAllComments" value="true" />
        </commentGenerator>
        
        
        
        
        
        
        <!--配置数据库连接信息-->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/test?useSSL=false&amp;characterEncoding=utf-8&amp;autoReconnect=true"
                        userId="root"
                        password="数据库密码">
            <property name="nullCatalogMeansCurrent" value="true"/>
        </jdbcConnection>
        

        <!--生成model 类，targetPackage 指定 model 类的包名，targetProject 指定
        生成的 model放在eclipse的哪个工程下面-->
        <javaModelGenerator targetPackage="com.example.demo.model"
                            targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
            <property name="trimStrings" value="false"/>
        </javaModelGenerator>
        
        
        
        
             
        <!--生成 MyBatis的Mapper.xml文件，targetPackage 指定 mapper.xml文件的包名，targetProject 指定生成的 mapper.xml放在 eclipse的哪个工程下面
        -->
        <sqlMapGenerator targetPackage="com.example.demo.mapper"
                         targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
        
        
                
        
        <!--生成 MyBatis的 Mapper接口类文件,targetPackage 指定 Mapper 接口类的包名，targetProject 指定生成的 Mapper 接口放在eclipse 的哪个工程下面
        -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.example.demo.mapper"
                             targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>
        
        
        
        <!--数据库表名及对应的Java模型类名-->
        <table tableName="student" domainObjectName="Student"
               enableCountByExample="false"
               enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false"
               selectByExampleQueryId="false" />
                       
        
        
        <!--
                <table tableName="user" domainObjectName="User"
                       enableCountByExample="false"
                       enableUpdateByExample="false"
                       enableDeleteByExample="false"
                       enableSelectByExample="false"
                       selectByExampleQueryId="false" />-->
                
        
    </context>
</generatorConfiguration>
```



然后使用插件自动生成代码。





