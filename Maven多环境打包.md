*Note: Directly copied from [https://www.jianshu.com/p/28e8f34455f7](https://www.jianshu.com/p/28e8f34455f7)*

# Maven多环境打包

## 场景

在应用部署的时候，往往遇到需要发布到不同环境的情况，而每个环境的数据库信息、密钥信息等可能会存在差异。举个例子，在Spring Boot中我们使用application.properties作为应用环境配置文件，那么不同环境下的配置可能有以下差异：

***dev(开发环境)***
```xml
spring.datasource.url=jdbc:oracle:thin:@192.168.0.1:1521:orcl 
spring.datasource.username=dev
spring.datasource.password=dev
```
**test(测试环境)**
```xml
spring.datasource.url=jdbc:oracle:thin:@192.168.0.1:1521:orcl 
spring.datasource.username=test
spring.datasource.password=test
```
**prod(生产环境)**
```xml
spring.datasource.url=jdbc:oracle:thin:@123.56.5.34:1521:orcl 
spring.datasource.username=prod
spring.datasource.password=prod
```
那么如何在打包的时候较好处理环境信息呢？Maven提供了一种比较优雅的方案处理这类问题。
下面针对Spring Boot框架开发的应用对如何配置多环境信息进行说明。

## 配置步骤

### 1.在pom中设置Filters

Spring Boot在它的Parent Pom中使用了以下默认配置：

```xml 
<resources>
    <resource>
          <directory>${basedir}/src/main/resources</directory>
        <filtering>true</filtering>
          <includes>
              <include>**/application*.yml</include>
              <include>**/application*.properties</include>
         </includes>
    </resource>
</resources>
```

(在Maven的Resource插件中，默认的filtering配置为false，此外Spring Boot默认为true)
上面配置的意思为满足/application.yml，/application.properties**两个正则表达式的所有文件都会进行过滤处理，且当filtering为true时过滤处理才生效，什么是过滤处理，请看下面步骤。

在pom文件的build标签下配置filters如下：

```xml
<build>
        <filters>  
            <filter>${basedir}/filters/application-dev.properties</filter>  
        </filters> 
</build>
```

filter里面内容的意思为：
假如src/main/resources/application.properties的内容为:

```xml
spring.datasource.url=@spring.datasource.url@
spring.datasource.username=@spring.datasource.username@
spring.datasource.password=@spring.datasource.password@
```
maven会在filters/application-dev.properties提取这些参数的值，假如application-${env}.properties的内容为：

```xml
spring.datasource.url=jdbc:oracle:thin:@192.168.0.1:1521:orcl 
spring.datasource.username=dev
spring.datasource.password=dev
```
当我们使用mvn package命令打包后，原来位置WEB-INF/classes下的application.properties中的值会被替换成application-dev.properties里面的内容。

### 2.配置profile(可选)

步骤1中的${env}参数，等价于pom下的profile id

```xml
<profiles>  
    <profile>
    <id>test</id>
    <activation>
        <activeByDefault>true</activeByDefault>
    </activation>
    </profile>
<profiles>  
```
当我们使用mvn package -Ptest命令打包应用时，系统会将application-test.properties的参数值复制到src/main/resources/application.properties中；
同理当我们使用mvn package -Pdev命令时，系统会将application-dev.properties的参数值复制到src/main/resources/application.properties中。

### 其他说明

根据网上的资料application.properties中的参数写法为 ${param}
但在Spring Boot 1.3.0.RELEASE中必须使用格式 @param@才能生效
详见：
[https://github.com/spring-projects/spring-boot/issues/980](https://github.com/spring-projects/spring-boot/issues/980),

[http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-use-short-command-line-arguments](http://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#howto-use-short-command-line-arguments)

### 2.除了使用${env}环境变量，还可以使用其他方法传递参数

通过mvn package -Dcustom=dev命令传入${custom}的参数值

通过pom中的<properties>属性定义参数值

### 3.除了在parent的pom.xml里面根据profile设置运行参数外，maven还支持在parent的pom.xml或 module的pom.xml设置profile，使得在某一种条件下激活一个profile并执行自定义的步骤：
```xml
        <profile>
            <id>webdev</id>
            <activation>
                <activeByDefault>false</activeByDefault>
                <property>
                    <name>deploy.mode</name>
                    <value>dev</value>
                </property>
            </activation>
            <build>
            </build>
        </profile>
```

意思为，当打包的mvn命令中含有deploy.mode参数，且参数值为dev时，激活这个profile。

例:mvn package -Ddeploy.mode=dev
