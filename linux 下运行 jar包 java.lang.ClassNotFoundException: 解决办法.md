### 1. 发现了这个常见的异常 
```
E:\test>java -cp . -jar x.jar 
Exception in thread "main" java.lang.NoClassDefFoundError: org/springframework/beans/factory/BeanFactory
Caused by: java.lang.ClassNotFoundException: org.springframework.beans.factory.BeanFactory
        at java.net.URLClassLoader$1.run(Unknown Source)
        at java.security.AccessController.doPrivileged(Native Method)
        at java.net.URLClassLoader.findClass(Unknown Source)
        at java.lang.ClassLoader.loadClass(Unknown Source)
        at sun.misc.Launcher$AppClassLoader.loadClass(Unknown Source)
        at java.lang.ClassLoader.loadClass(Unknown Source)
        at java.lang.ClassLoader.loadClassInternal(Unknown Source)
```
### 2. 经查找，发现正确的MANIFEST.MF如下 
```
Manifest-Version: 1.0
Main-Class: net.java2000.test.jar.TestJar
Class-Path: spring.jar 
 lib/commons-logging-1.1.jar
```
这里特别说明一下 

- 1)在 Class-Path: 后面有一个空格，切记 
- 2)在 Class-Path: 后面写上你的jar 用空格分开 
- 3)如果需要换行，切记在上一行末尾一定要有一个空格，下一行的开头一定要有一个空格 
- 4)最后一行要是一个空行，否则Eclipse打包时有可能把你的Class-Path 给忽略掉  

### 3. 结论 

1. 使用 java -cp 来设置 classpath 对于 jar来说是无效的，因为根据jar的安全规定，其内部的Class-Path 会起作用，外部的会被屏蔽掉(注意是屏蔽掉，不是覆盖掉) 
2. Java自身提供了一个设置classpath的方案，那就是使用命令行参数 
```
- Xbootclasspath:      完全取代基本核心的Java class 搜索路径.  
                                    不常用,否则要重新写所有Java 核心class  
- Xbootclasspath/a: 后缀在核心class搜索路径后面.常用!!  
- Xbootclasspath/p: 前缀在核心class搜索路径前面.不常用,避免  
                                    引起不必要的冲突.  
```
语法如下:  (分隔符与classpath参数类似，unix使用:号,windows使用;)  
```java -Xbootclasspath/a:spring.jar;lib/commons-logging-1.1.jar -jar MyProject.jar  ```


3. 当然，你把jar放到 {Java_home}\jre\lib\ext 这个目录下面也是可以的，应为JVM肯定会搜索这个目录
