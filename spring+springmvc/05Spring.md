核心模块：

spring-core:依赖注入IOC与DI的基本实现

spring-beans:Bean工厂与Bean的装配

spring-context:定义基础的spring的context上下文，即IOC容器

spring-context-support:对spring的IOC容器的扩展和支持

spring-context-indexer:spring 的类管理组件和classPath扫描

spring-expression:spring表达式语言



切面编程：

spring-aop:面向切面编程的应用模块

spring-aspects:集成aspectJ，AOP的应用框架

spring-instrument:动态Class Loading模块



数据访问与集成：

spring-jdbc:

spring-tx:

spring-orm:

spring-oxm:java对象与xml的相互转化

spring-jms:发送与接收消息



web组件：

spring-web:提供最基础的web支持，主要建立于核心容器之上，通过Servlet与Linster来初始化IOC容器

spring-webmvc:

spring-websocket:

spring-webflux:



通信报文：

spring-messaging:从spring4开始新加入的一个模块，主要职责是为spring框架集成一些基础的报文传送应用



集成测试：

spring-test:



集成兼容：

spring-framework-bom:解决spring的不同模块依赖版本不同问题



![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/20200315040833.png)



 

### spring流程原理

BeanFactory

BeanDefinition

BeanDefinitionReader



spring ioc 容器初始化

- 定位：定位配置文件和扫描相关的注解
- 加载：将配置信息载入到内存中
- 注册：根据载入的信息，将对象初始化到ioc容器中





 https://www.cnblogs.com/zhangfengxian/p/11072500.html 