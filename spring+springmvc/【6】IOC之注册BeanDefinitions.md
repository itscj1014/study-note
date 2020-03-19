在获取到**Document对象**和**Resource资源对象**后，doLoadBeanDefinitions方法中会调用**registerBeanDefinitions(doc, resource)**,开始注册BeanDefinitions



~~~java
private final BeanDefinitionRegistry registry;	

public int registerBeanDefinitions(Document doc, Resource resource) throws BeanDefinitionStoreException {
    //创建BeanDefinitionDocumentReader对象
		BeanDefinitionDocumentReader documentReader = createBeanDefinitionDocumentReader();
    //获取已注册BeanDefinition数量
		int countBefore = getRegistry().getBeanDefinitionCount();
    //创建XmlReaderContext对象
    //注册BeanDefinition
		documentReader.registerBeanDefinitions(doc, createReaderContext(resource));
    //计算新注册的BeanDefinition数量
		return getRegistry().getBeanDefinitionCount() - countBefore;
	}
~~~



