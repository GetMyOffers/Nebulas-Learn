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
<Configuration status="debug">
    <Appenders>
        <Console name="SYSOUT" target="SYSTEM_OUT">
            <PatternLayout patter="%d [%t] %-5p [%c] - %m%n  "/>
        </Console>

        <!-- name可以自定义，作用是在Loggers中AppenderRef中使用 -->
        <!-- fileName定义输出文件名称（当前文件） -->
        <!-- filePattern定义输出文件名称（文件满足条件后自动截断，生成历史文件） -->
        <RollingFile name="DEBUG_ROLLING_FILE"
                     fileName="/Users/irving/IdeaProjects/SpringBoot-Study/logs/logs.logs"
                     filePattern="/Users/irving/IdeaProjects/SpringBoot-Study/logs/%d{yyyy-MM-dd}-debugs.log">
            <PatternLayout>
                <Pattern>%d [%t] %-5p [%c] - %m%n  </Pattern>
            </PatternLayout>

            <!-- 文件截断的条件，具体参考文档 -->
            <Policies>
                <TimeBasedTriggeringPolicy interval="24"/>
                <SizeBasedTriggeringPolicy size="250 MB"/>
            </Policies>
        </RollingFile>

        <!-- 同一来源的Appender可以定义多个 -->
        <RollingFile name="ERROR_ROLLING_FILE"
                     fileName="/Users/irving/IdeaProjects/SpringBoot-Study/logs/error-logs.logs"
                     filePattern="/Users/irving/IdeaProjects/SpringBoot-Study/logs/%d{yyyy-MM-dd}-error.log">
            <!-- 可以通过该参数来设置获取日志的权限 -->
            <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout>
                <Pattern>%d [%t] %-5p [%c] - %m%n  </Pattern>
            </PatternLayout>
            <Policies>
                <TimeBasedTriggeringPolicy interval="24"/>
                <SizeBasedTriggeringPolicy size="250 MB"/>
            </Policies>
        </RollingFile>
    </Appenders>

    <Loggers>
        <Root level="debug">
            <AppenderRef ref="SYSOUT"/>
            <AppenderRef ref="DEBUG_ROLLING_FILE"/>
            <AppenderRef ref="ERROR_ROLLING_FILE"/>
        </Root>
    </Loggers>
</Configuration>
<!-- /Users/irving/IdeaProjects/SpringBoot-Study -->

```
