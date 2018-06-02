### Spring IOC

 

要了解**控制反转( Inversion of Control )**, 有必要先了解软件设计的一个重要思想：**依赖倒置原则（Dependency Inversion Principle ）**。

**依赖倒置**：从高层谈需求，一层层往下实现。在代码上的体现为把底层类作为参数传入上层类，实现上层类对下层类的控制。

这样做的好处就是如果我们想要改变底层类的定义的时候，上层类并不会受到影响，因为上层类不关心下层是怎么实现的，只要它能够拿到下层类的实例化对象即可。

但是想想看，如果我想要一个车子，那么我得先要实例化一个车身类，要得到车身又得要一个底盘类，依次下去，轮子类，螺丝类，多么繁琐啊，并且依赖关系多了我们根本记不住依赖关系啊，所以 Spring 帮助我们做了这一步，我们要做的只是在配置文件中提前写好依赖关系即可，Spring 会返回一个我们想要的汽车实例。

那么，Spring 怎么做到的呢？我做了一个图，如下：

![](https://github.com/Lisanaaa/Nebulas-Learn/blob/master/image/Spring-Bean-Create.png)



#### 总结

Spring IOC 容器主要有继承体系底层的 BeanFactory、高层的 ApplicationContext 和 WebApplicationContext

Bean 有自己的生命周期

**容器启动原理: **

Spring 应用的 IOC 容器通过 tomcat 的 Servlet 或 Listener 监听启动加载；Spring MVC 的容器由 DispatchServlet 作为入口加载；Spring 容器是 Spring MVC 容器的父容器

**容器加载 Bean 原理：**

BeanDefinitionReader 读取 Resource 所指向的配置文件资源，然后解析配置文件。配置文件中每一个解析成一个 BeanDefinition 对象，并保存到 BeanDefinitionRegistry 中；

容器扫描 BeanDefinitionRegistry 中的 BeanDefinition；调用 InstantiationStrategy 进行Bean 实例化的工作；使用 BeanWrapper 完成 Bean 属性的设置工作；

单例 Bean 缓存池：Spring 在 DefaultSingletonBeanRegistry 类中提供了一个用于缓存单实例 Bean 的缓存器，它是一个用 HashMap 实现的缓存器，单实例的 Bean 以 beanName 为键保存在这个 HashMap 中。

 

 #### References

1. [Spring IOC原理总结](https://www.jianshu.com/p/9fe5a3c25ab6)
2. [Spring IoC有什么好处呢？](https://www.zhihu.com/question/23277575)
3. [Inversion of Control Containers and the Dependency Injection pattern](https://martinfowler.com/articles/injection.html)
4. [Dependency Injection](https://en.wikipedia.org/wiki/Dependency_injection)

 

 

 

