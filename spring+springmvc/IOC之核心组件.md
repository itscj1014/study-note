### 核心组件

1. Resource体系： **org.springframework.core.io.Resource** ，对资源的抽象，它的每一个实现类都代表了一种资源的访问策略，如ClassPathResource,FileSystemResource等![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/InputStreamResource.png)

   

2. ResourceLoader体系：有了资源，就应该有资源加载，Spring利用  **org.springframework.core.io.ResourceLoader** 来进行统一资源加载

   ![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/ResourceLoader.png)

   

3. BeanFactory体系： **org.springframework.beans.factory.BeanFactory**  是一个非常纯粹的bean容器，它是ioc的必备结构，其中**BeanDefinition**是它最基本的结构。BeanFactory内部维护了一个**BeanDefinition** map,并可以根据 **BeanDefinition** 的描述进行bean的创建和管理。 BeanFactory 有三个直接子类 ListableBeanFactory、HierarchicalBeanFactory 和 AutowireCapableBeanFactory 。 **DefaultListableBeanFactory** 为最终默认实现，它实现了所有接口 



4. BeanDefinition体系： **org.springframework.beans.factory.config.BeanDefinition** ，用来描述Spring中的bean 对象

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/BeanDefinition.png)





5. BeanDefinitionReader体系： **org.springframework.beans.factory.support.BeanDefinitionReader** ，作用是读取Spring配置文件的内容 ，并将其转化为IOC容器内部的数据结构：BeanDefinition

![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/BeanDefinitionReader.png)



6. ApplicationContext体系：  **org.springframework.context.ApplicationContext**  ，大名鼎鼎的Spring容器，也叫做应用上下文，继承自BeanFactory,但是又不同于BeanFactory:
   - 继承 `org.springframework.context.MessageSource` 接口，提供国际化的标准访问策略。
   - 继承  org.springframework.context.ApplicationEventPublisher 接口，提供强大的事件机制
   - 扩展 ResourceLoader,可以用来加载多种Resource,可以灵活访问多种资源
   - 对Web应用的支持



![](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/ClassPathXmlApplicationContext.png)

