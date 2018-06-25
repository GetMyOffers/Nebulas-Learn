# 配置log4j2

## 1. 在Spring Boot中使用log4j2
+ SpringBoot默认使用了logback，所以要使用log4j2，要先去除spring boot的默认logging包

```xml
<!-- 去除spring-boot默认日志依赖包 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>${spring.boot.starter.version}</version>
    <exclusions>
        <!-- 想要配置log4j2，就要先去除logging包 -->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

+ 添加spring-boot-starter-log4j2的依赖包

```xml
<!-- 添加spring-boot-starter-log4j2的依赖包 -->
<!-- day04 使用log4j2添加包，此时版本是log4j 2.6.2 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
    <version>${spring.boot.starter.version}</version>
</dependency>
```

+ 在application.properties中设置log4j2配置文件路径

```properties
logging.config= src/main/resources/log4j2.xml
```

+ 配置log4j2

## 2. 如何配置log4j2

### 2.1. 参考资料
+ [log4j2 documents](http://logging.apache.org/log4j/2.x/index.html)

### 2.2. log4j2配置
+ Appenders：
    + 作用：定义日志输出位置（控制台、文件或其他）、内容结构（起始就是PatternLayout）、日志等级（不完全由Appenders确定）。
    + 不能做：哪些类使用该Appender
+ Loggers:
    + 作用：定义哪些类使用那些Appender，并可以设置日志级别

### 2.3. 举例
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="info">
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout patter="%m%n"/>
        </Console>

        <!-- name可以自定义，作用是在Loggers中AppenderRef中使用 -->
        <!-- fileName定义输出文件名称（当前文件） -->
        <!-- filePattern定义输出文件名称（文件满足条件后自动截断，生成历史文件） -->
        <RollingFile name="WARN_ROLLING_FILE"
                     fileName="/Users/lisanaaa/Desktop/app/nebulasio/logs/user-center/console-warn.log"
                     filePattern="/Users/lisanaaa/Desktop/app/nebulasio/logs/user-center/console-warn.%d{yyyy-MM-dd}.log">
            <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout>
                <Charset>UTF-8</Charset>
                <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
            </PatternLayout>

            <!-- 文件截断的条件，具体参考文档 -->
            <Policies>
                <TimeBasedTriggeringPolicy interval="24"/>
                <SizeBasedTriggeringPolicy size="250 MB"/>
            </Policies>
            <DefaultRolloverStrategy>
                <Delete basePath="/Users/lisanaaa/Desktop/app/nebulasio/logs/user-center/" maxDepth="2">
                    <IfFileName glob="*.log" />
                    <IfLastModified age="60d" />
                </Delete>
            </DefaultRolloverStrategy>
        </RollingFile>

        <!-- 同一来源的Appender可以定义多个 -->
        <RollingFile name="ERROR_ROLLING_FILE"
                     fileName="/Users/lisanaaa/Desktop/app/nebulasio/logs/user-center/console-error.log"
                     filePattern="/Users/lisanaaa/Desktop/app/nebulasio/logs/user-center/console-error.%d{yyyy-MM-dd}.log">
            <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout>
                <Charset>UTF-8</Charset>
                <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
            </PatternLayout>

            <!-- 文件截断的条件，具体参考文档 -->
            <Policies>
                <TimeBasedTriggeringPolicy interval="24"/>
                <SizeBasedTriggeringPolicy size="250 MB"/>
            </Policies>
            <DefaultRolloverStrategy>
                <Delete basePath="/Users/lisanaaa/Desktop/app/nebulasio/logs/user-center/" maxDepth="2">
                    <IfFileName glob="*.log" />
                    <IfLastModified age="60d" />
                </Delete>
            </DefaultRolloverStrategy>
        </RollingFile>

        <!-- 同一来源的Appender可以定义多个 -->
        <RollingFile name="INFO_ROLLING_FILE"
                     fileName="/Users/lisanaaa/Desktop/app/nebulasio/logs/user-center/console-info.log"
                     filePattern="/Users/lisanaaa/Desktop/app/nebulasio/logs/user-center/console-info.%d{yyyy-MM-dd}.log">
            <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout>
                <Charset>UTF-8</Charset>
                <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
            </PatternLayout>

            <!-- 文件截断的条件，具体参考文档 -->
            <Policies>
                <TimeBasedTriggeringPolicy interval="24"/>
                <SizeBasedTriggeringPolicy size="250 MB"/>
            </Policies>
            <DefaultRolloverStrategy>
                <Delete basePath="/Users/lisanaaa/Desktop/app/nebulasio/logs/user-center/" maxDepth="2">
                    <IfFileName glob="*.log" />
                    <IfLastModified age="60d" />
                </Delete>
            </DefaultRolloverStrategy>
        </RollingFile>
    </Appenders>

    <Loggers>
        <logger name="com.atomikos" level="info"/>
        <logger name="org.springframework" level="info"/>
        <logger name="org.mybatis" level="info"/>
        <logger name="org.apache" level="info"/>

        <Root level="info">
            <AppenderRef ref="STDOUT"/>
            <AppenderRef ref="WARN_ROLLING_FILE"/>
            <AppenderRef ref="ERROR_ROLLING_FILE"/>
            <AppenderRef ref="INFO_ROLLING_FILE"/>
        </Root>
    </Loggers>
</Configuration>
```
