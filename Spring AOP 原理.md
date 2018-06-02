AOP为Aspect Oriented Programming的缩写，意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是Spring框架中的一个重要内容，它通过对既有程序定义一个切入点，然后在其前后切入不同的执行内容，比如常见的有：打开数据库连接/关闭数据库连接、打开事务/关闭事务、记录日志等。基于AOP不会破坏原来程序逻辑，因此它可以很好的对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

下面主要讲两个内容，一个是如何在Spring Boot中引入Aop功能，二是如何使用Aop做切面去统一处理Web请求的日志。

以下所有操作基于[chapter4-2-2工程](http://git.oschina.net/didispace/SpringBoot-Learning)进行。

## 准备工作

因为需要对web请求做切面来记录日志，所以先引入web模块，并创建一个简单的hello请求的处理。

- `pom.xml`中引入web模块

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

- 实现一个简单请求处理：通过传入name参数，返回“hello xxx”的功能。

```java
@RestController
public class HelloController {

    @RequestMapping(value = "/hello", method = RequestMethod.GET)
    @ResponseBody
    public String hello(@RequestParam String name) {
        return "Hello " + name;
    }

}
```

下面，我们可以对上面的/hello请求，进行切面日志记录。

## 引入AOP依赖

在Spring Boot中引入AOP就跟引入其他模块一样，非常简单，只需要在`pom.xml`中加入如下依赖：

```java

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

在完成了引入AOP依赖包后，一般来说并不需要去做其他配置。也许在Spring中使用过注解配置方式的人会问是否需要在程序主类中增加`@EnableAspectJAutoProxy`来启用，实际并不需要。

可以看下面关于AOP的默认配置属性，其中`spring.aop.auto`属性默认是开启的，也就是说只要引入了AOP依赖后，默认已经增加了`@EnableAspectJAutoProxy`。

 

```java
# AOP
spring.aop.auto=true # Add @EnableAspectJAutoProxy.
spring.aop.proxy-target-class=false # Whether subclass-based (CGLIB) proxies are to be created (true) as
 opposed to standard Java interface-based proxies (false).

```

而当我们需要使用CGLIB来实现AOP的时候，需要配置`spring.aop.proxy-target-class=true`，不然默认使用的是标准Java的实现。

## 实现Web层的日志切面

实现AOP的切面主要有以下几个要素：

- 使用`@Aspect`注解将一个java类定义为切面类

- 使用`@Pointcut`定义一个切入点，可以是一个规则表达式，比如下例中某个package下的所有函数，也可以是一个注解等。

- 根据需要在切入点不同位置的切入内容

  - 使用`@After`在切入点结尾处切入内容

  - 使用`@Before`在切入点开始处切入内容

  - 使用`@After`在切入点结尾处切入内容

  - 使用`@AfterReturning`在切入点return内容之后切入内容（可以用来对处理返回值做一些加工处理）

  - 使用`@Around`在切入点前后切入内容，并自己控制何时执行切入点自身的内容

  - 使用`@AfterThrowing`用来处理当切入内容部分抛出异常之后的处理逻辑

     

   

  ```java
  @Aspect
  @Component
  public class WebLogAspect {
  
      private Logger logger = Logger.getLogger(getClass());
  
      @Pointcut("execution(public * com.didispace.web..*.*(..))")
      public void webLog(){}
  
      @Before("webLog()")
      public void doBefore(JoinPoint joinPoint) throws Throwable {
          // 接收到请求，记录请求内容
          ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
          HttpServletRequest request = attributes.getRequest();
  
          // 记录下请求内容
          logger.info("URL : " + request.getRequestURL().toString());
          logger.info("HTTP_METHOD : " + request.getMethod());
          logger.info("IP : " + request.getRemoteAddr());
          logger.info("CLASS_METHOD : " + joinPoint.getSignature().getDeclaringTypeName() + "." + joinPoint.getSignature().getName());
          logger.info("ARGS : " + Arrays.toString(joinPoint.getArgs()));
  
      }
  
      @AfterReturning(returning = "ret", pointcut = "webLog()")
      public void doAfterReturning(Object ret) throws Throwable {
          // 处理完请求，返回内容
          logger.info("RESPONSE : " + ret);
      }
  
  }
  ```

  可以看上面的例子，通过`@Pointcut`定义的切入点为`com.didispace.web`包下的所有函数（对web层所有请求处理做切入点），然后通过`@Before`实现，对请求内容的日志记录（本文只是说明过程，可以根据需要调整内容），最后通过`@AfterReturning`记录请求返回的对象。

  通过运行程序并访问：`http://localhost:8080/hello?name=didi`，可以获得日志输出

  #### 优化：AOP切面中的同步问题

  在WebLogAspect切面中，分别通过doBefore和doAfterReturning两个独立函数实现了切点头部和切点返回后执行的内容，若我们想统计请求的处理时间，就需要在doBefore处记录时间，并在doAfterReturning处通过当前时间与开始处记录的时间计算得到请求处理的消耗时间。

  那么我们是否可以在WebLogAspect切面中定义一个成员变量来给doBefore和doAfterReturning一起访问呢？是否会有同步问题呢？

  的确，直接在这里定义基本类型会有同步问题，所以我们可以引入ThreadLocal对象，像下面这样进行记录：

  ```java
  @Aspect
  @Component
  public class WebLogAspect {
  
      private Logger logger = Logger.getLogger(getClass());
  
      ThreadLocal<Long> startTime = new ThreadLocal<>();
  
      @Pointcut("execution(public * com.didispace.web..*.*(..))")
      public void webLog(){}
  
      @Before("webLog()")
      public void doBefore(JoinPoint joinPoint) throws Throwable {
          startTime.set(System.currentTimeMillis());
  
          // 省略日志记录内容
      }
  
      @AfterReturning(returning = "ret", pointcut = "webLog()")
      public void doAfterReturning(Object ret) throws Throwable {
          // 处理完请求，返回内容
          logger.info("RESPONSE : " + ret);
          logger.info("SPEND TIME : " + (System.currentTimeMillis() - startTime.get()));
      }
  
  
  }
  ```

  #### 优化：AOP切面的优先级

  由于通过AOP实现，程序得到了很好的解耦，但是也会带来一些问题，比如：我们可能会对Web层做多个切面，校验用户，校验头信息等等，这个时候经常会碰到切面的处理顺序问题。

  所以，我们需要定义每个切面的优先级，我们需要`@Order(i)`注解来标识切面的优先级。**i的值越小，优先级越高**。假设我们还有一个切面是`CheckNameAspect`用来校验name必须为didi，我们为其设置`@Order(10)`，而上文中WebLogAspect设置为`@Order(5)`，所以WebLogAspect有更高的优先级，这个时候执行顺序是这样的：

  - 在`@Before`中优先执行`@Order(5)`的内容，再执行`@Order(10)`的内容
  - 在`@After`和`@AfterReturning`中优先执行`@Order(10)`的内容，再执行`@Order(5)`的内容

  所以我们可以这样子总结：

  - 在切入点前的操作，按order的值由小到大执行
  - 在切入点后的操作，按order的值由大到小执行

   