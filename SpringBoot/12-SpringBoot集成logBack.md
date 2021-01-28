# 12-SpringBoot集成logBack

SpringBoot官方推荐优先使用带有`-spring`的文件名作为日志配置(如使用logback-spring.xml)，默认放在src/main/resources目录下。如果不想用默认文件名格式和默认目录，可以通过配置文件的`logging.config`属性来指定自定义的名字。

`logging-config=classpath:logging-config.xml`。

日志内容为：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 日志级别从低到高分为 TRACE < DEBUG < INFO < WARN < ERROR < FATAL，
如果设置为 WARN，则低于 WARN 的信息都不会输出 -->
<!-- scan:当此属性设置为 true 时，配置文件如果发生改变，将会被重新加载，默认值为
true -->
<!-- scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认
单位是毫秒。当 scan 为 true 时，此属性生效。默认的时间间隔为 1 分钟。 -->
<!-- debug:当此属性设置为 true 时，将打印出 logback 内部日志信息，实时查看 logback
运行状态。默认值为 false。通常不打印 -->

<configuration scan="true" scanPeriod="100 seconds">


    <!--输出到控制台-->
    <appender name="CONSOLE"
              class="ch.qos.logback.core.ConsoleAppender">
        <!--此日志 appender 是为开发使用，只配置最底级别，控制台输出的日志级别是大
       于或等于此级别的日志信息-->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>debug</level>
        </filter>
        <!--
            %date:时间，精确到毫秒,%d也可以
            %-5p:日志级别，-代表左对齐，5代表占位符有5个
            %thread:表示哪个线程输出的日志
            %logger(60):代表类路径，60表示最长字符串长度
            %file:表示文件名
            %line:行数
            %msg:表示日志信息
            %n:换行符
          -->
        <encoder>
            <Pattern>%d [%-5p] %msg%n</Pattern>
            <!-- 设置字符集 -->
            <charset>UTF-8</charset>
        </encoder>
    </appender>

    <!--输出到文件-->
    <appender name="FILE"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!--<File>/home/log/stdout.log</File>-->
        <File>D:/log/stdout.log</File>
        <!--
            %date:时间，精确到毫秒
            %-5p:日志级别，-代表左对齐，5代表占位符有5个
            %thread:表示哪个线程输出的日志
            %logger(60):代表类路径，60表示最长字符串长度
            %file:表示文件名
            %line:行数
            %msg:表示日志信息
            %n:换行符
        -->
        <encoder>
            <pattern>%date [%-5p] [%thread] %logger{60}
                [%file : %line] %msg%n</pattern>
        </encoder>
        <rollingPolicy
                class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- 添加.gz 历史日志会启用压缩 大大缩小日志文件所占空间 -->

            <!--<fileNamePattern>/home/log/stdout.log.%d{yyyy-MM-dd}.log</fileNam
            ePattern>-->

            <fileNamePattern>D:/log/stdout.log.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>30</maxHistory><!-- 保留 30 天日志 -->
        </rollingPolicy>
    </appender>


    <!--单个设置，设置数据持久层的level-->
    <logger name="com.abc.springboot.mapper" level="DEBUG" />

    <!--使用root标签引用，才能生效。全局设置-->
    <root level="INFO">
        	<appender-ref ref="CONSOLE"/>
	        <appender-ref ref="FILE"/>
    </root>
</configuration>

```



如果要自己打印日志。需要引入lombok依赖，在需要打印的类上加注解`@Slf4j`。

然后使用该依赖的`log`来打印不同级别的日志。

<img src="https://crayon-1302863897.cos.ap-beijing.myqcloud.com/image/image-20210127151636088.png" alt="image-20210127151636088" style="zoom:67%;" />



