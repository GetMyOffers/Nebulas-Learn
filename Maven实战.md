# POM Sample

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>io.nebulas</groupId>
        <artifactId>wallet-parent</artifactId>
        <version>1.0-SNAPSHOT</version>
        <relativePath/>
    </parent>

    <artifactId>internal-web</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <build>
        <finalName>${project.artifactId}</finalName>
        <filters>
            <filter>src/main/filter/application-${project.environment}.filter</filter>
        </filters>
        <resources>
            <resource>
                <filtering>true</filtering>
                <directory>src/main/resources/</directory>
                <includes>
                    <include>*.properties</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
            <resource>
                <directory>${project.basedir}/src/main/resources</directory>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <encoding>${project.build.sourceEncoding}</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>io.nebulas</groupId>
            <artifactId>user-center-service</artifactId>
            <version>1.1.3-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>io.nebulas</groupId>
            <artifactId>wallet-service</artifactId>
            <version>1.1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>bootstrap</artifactId>
        </dependency>
        <dependency>
            <groupId>org.webjars</groupId>
            <artifactId>jquery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
        </dependency>
        <!--spring boot-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
        <!--validator-->
        <dependency>
            <groupId>javax.validation</groupId>
            <artifactId>validation-api</artifactId>
        </dependency>
        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <!--mysql-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
        </dependency>

        <!--utils-->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-collections4</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
        </dependency>
        <dependency>
            <groupId>joda-time</groupId>
            <artifactId>joda-time</artifactId>
        </dependency>
        <!--fastjson-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
        </dependency>
        <!--guava-->
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
        </dependency>
        <!--swagger-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>com.fasterxml.jackson.core</groupId>
                    <artifactId>jackson-annotations</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
        </dependency>
        <!--ok http-->
        <dependency>
            <groupId>com.squareup.okhttp3</groupId>
            <artifactId>okhttp</artifactId>
        </dependency>
        <dependency>
            <groupId>com.squareup.okhttp3</groupId>
            <artifactId>logging-interceptor</artifactId>
        </dependency>
        <dependency>
            <groupId>com.squareup.retrofit2</groupId>
            <artifactId>retrofit</artifactId>
        </dependency>
        <dependency>
            <groupId>com.squareup.retrofit2</groupId>
            <artifactId>adapter-rxjava</artifactId>
        </dependency>
        <dependency>
            <groupId>com.squareup.retrofit2</groupId>
            <artifactId>converter-simplexml</artifactId>
        </dependency>
        <dependency>
            <groupId>com.squareup.retrofit2</groupId>
            <artifactId>converter-gson</artifactId>
        </dependency>
        <dependency>
            <groupId>org.simpleframework</groupId>
            <artifactId>simple-xml</artifactId>
        </dependency>
        <!--grpc-->
        <dependency>
            <groupId>io.grpc</groupId>
            <artifactId>grpc-all</artifactId>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <!--test-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

# POM 的基本内容

![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%20POM%20%E5%9F%BA%E6%9C%AC%E5%86%85%E5%AE%B91.jpeg)

![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%20POM%20%E5%9F%BA%E6%9C%AC%E5%86%85%E5%AE%B92.jpeg)

# 构建项目基本的 Maven 命令

1. mvn clean
2. mvn compile
3. mvn test
4. mvn package
5. mvn install
6. mvn dependency:list
7. mvn dependency:tree
8. mvn dependency:analyze
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E4%BE%9D%E8%B5%96analyze.jpeg)


# Maven 坐标

![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E5%9D%90%E6%A0%871.jpeg)
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E5%9D%90%E6%A0%872.jpeg)
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E5%9D%90%E6%A0%873.jpeg)
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E5%9D%90%E6%A0%874.jpeg)

# Maven 依赖范围

![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E4%BE%9D%E8%B5%96%E8%8C%83%E5%9B%B41.jpeg)
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E4%BE%9D%E8%B5%96%E8%8C%83%E5%9B%B42.jpeg)
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E4%BE%9D%E8%B5%96%E8%8C%83%E5%9B%B43.jpeg)
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E4%BE%9D%E8%B5%96%E8%8C%83%E5%9B%B44.jpeg)


# Maven 可选依赖

![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E5%8F%AF%E9%80%89%E4%BE%9D%E8%B5%961.jpeg)
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E5%8F%AF%E9%80%89%E4%BE%9D%E8%B5%962.jpeg)
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E5%8F%AF%E9%80%89%E4%BE%9D%E8%B5%963.jpeg)
![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E5%8F%AF%E9%80%89%E4%BE%9D%E8%B5%964.jpeg)

# Maven 排除依赖

![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Maven%E6%8E%92%E9%99%A4%E4%BE%9D%E8%B5%96.jpeg)



