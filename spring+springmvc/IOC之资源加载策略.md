资源加载策略需满足的条件：

- 职能划分清楚，资源的定义和资源的加载应该要有一个清晰的界限
- 统一的抽象，统一的资源定位和资源加载策略。资源加载过后要返回统一的抽象给客户端，客户端要对资源进行怎样的处理，应该由抽象资源接口来界定



### 统一资源：Resource

 **org.springframework.core.io.Resource** 为Spring框架所有资源的抽象和接口访问。作为所有资源的统一抽象，Resource定义了一些通用的方法，由子类 **AbstractResource** 提供默认的实现



### 统一资源定位：ResourceLoader

Spring将资源的统一定位和统一加载分开了，**Resource**定义了统一的资源，那么资源的加载则由**ResourceLoader**来统一定义。

  **org.springframework.core.io.ResourceLoader** 为Spring资源加载的统一抽象，具体的资源加载则由相应的实现类完成，所以我们可以将ResourceLoader称为统一资源定位器。**主要应用于根据给定的资源文件地址，返回对应的Resource**



#### 子类结构

- **DefaultResourceLoader**
  - 构造方法
  - getResource方法
  - ProtocolResolver：允许用户自定义资源加载协议，然后调用DefaultResourceLoader.addProtocolResolver(ProtocolResolver) 方法即可
- **FileSystemResourceLoader**
- **ResourcePatternResolver**
  - 在 ResourceLoader 的基础上增加了 #getResources(String locationPattern) 方法，以支持根据路径匹配模式返回多个 Resource 实例
  - 新增了一种新的协议前缀 "classpath*:"，该协议前缀由其子类负责实现
  - **PathMatchingResourcePatternResolver** 是一个集大成者的 ResourceLoader ，因为它即实现了 Resource getResource(String location) 方法，也实现了 Resource[] getResources(String locationPattern) 方法。





