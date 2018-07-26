### 1. 问题：

SpringBoot本身需要引入自身的一个parent,但是pom里面一般都已经存在了一个parent，这时就不能在引入springBoot的parent


解决方案：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>1.3.3.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>1.5.6.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

### 2. 异常：

```Exception in thread "main" java.lang.IllegalArgumentException: Cannot instantiate interface org.springframework.context.ApplicationListener : org.springframework.boot.logging.ClasspathLoggingApplicationListener```


```Caused by: java.lang.NoClassDefFoundError: org/springframework/context/event/GenericApplicationListener```

解决方案：

这个问题可能是由于Spring的版本低导致，升级spring版本。亲测到4.2.5.RELEASE可以
<org.springframework.version>4.2.5.RELEASE</org.springframework.version>


### 3. 异常：


```
Exception in thread "main" java.lang.IllegalArgumentException: LoggerFactory is not a Logback LoggerContext but Logback is on the classpath. Either remove Logback or the competing implementation (class org.slf4j.impl.Log4jLoggerFactory loaded from file:/D:/maven/repository/org/slf4j/slf4j-log4j12/1.7.5/slf4j-log4j12-1.7.5.jar). If you are using WebLogic you will need to add 'org.slf4j' to prefer-application-packages in WEB-INF/weblogic.xml Object of class [org.slf4j.impl.Log4jLoggerFactory] must be an instance of class ch.qos.logback.classic.LoggerContext
at org.springframework.util.Assert.isInstanceOf(Assert.java:346)
at org.springframework.boot.logging.logback.LogbackLoggingSystem.getLoggerContext(LogbackLoggingSystem.java:221)
```

解决方案：
这个异常是由于打印日志的jar冲突导致，SpringBoot本身有打印日志的功能，如果跟本地的冲突，就需要去掉，如下

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>1.3.3.RELEASE</version>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### 4. 异常：


```
Cannot instantiate factory class: org.springframework.boot.autoconfigure.AutoConfigurationImportFilter

Caused by: java.lang.IllegalAccessException: Class org.springframework.core.io.support.SpringFactoriesLoader can not access a member of class org.springframework.boot.autoconfigure.condition.OnClassCondition with modifiers ""
```

解决方案：

这种可以检查org.springframework.boot的版本，可能是版本不兼容，我这里一开始设置的1.5.6.RELEASE，一直出异常，后来SpringBoot的版本改成全部改成1.3.3.RELEASE  把Spring的版本改成4.2.5.RELEASE，通过


### 5. 异常：

SpringBoot启动

```xport.annotation.AnnotationMBeanExporter -Unregistering JMX-exposed beans on shutdown ，tomcat也没有运行```


可能原因1：

看看是否屏蔽了，检查 <artifactId>spring-boot-starter-web</artifactId> 这个下面是否屏蔽了spring-boot-starter-tomcat，
如果有，请去掉
```
<exclusion>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
</exclusion>
```

可能原因2：
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-tomcat</artifactId>
    <scope>provided</scope>
</dependency>
```
将<scope>provided</scope>注释掉

### 6. 异常：


```Caused by: java.lang.NoClassDefFoundError: org/springframework/beans/factory/ObjectProvider
Caused by: java.lang.ClassNotFoundException: org.springframework.beans.factory.ObjectProvider```

解决方案

这个异常最蛋疼，搞了半天是SpringBoot的版本不兼容，切换了一个版本到1.3.3.RELEASE 这里就好了
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>1.3.3.RELEASE</version>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

建议把<groupId>org.springframework.boot</groupId>这下下面的版本都改成1.3.3.RELEASE


### 7. 异常：

```
java.lang.NoSuchMethodError: org.springframework.expression.spel.SpelParserConfiguration.<init>(Lorg/springframework/expression/spel/SpelCompilerMode;Ljava/lang/ClassLoader
```
解决方案：

缺少jar

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-expression</artifactId>
    <version>4.2.5.RELEASE</version>
</dependency>
