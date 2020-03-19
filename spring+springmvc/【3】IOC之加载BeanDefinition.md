资源定位->装载->注册

### 装载

装载就是BeanDefinition的载入。BeanDefinitionReader读取解析Resource资源，也就是将用户定义的Bean表示成IOC容器的内部数据结构：BeanDefinition

- 在IOC容器内部，维护着一个BeanDefinition Map 的结构

- 配置文件中每一个<bean> 都对应着一个BeanDefinition对象

BeanDefinitionReader：主要定义资源文件的读取，并转化为 BeanDefinition 的各个功能

### 注册

向IOC容器注册已经解析好的BeanDefinition，这个过程是通过**BeanDefinitionRegistry**来实现的。

在IOC容器内部其实是将上一步解析得到的BeanDefinition注入到一个HashMap容器中。IOC容器就是通过这个HashMap来维护这些BeanDefinition的

- 这个过程并没有完成依赖注入（Bean创建），Bean的创建发生的第一次调用 getBean()方法，向容器索要Bean时
- 我们也可以设置预处理，即对某个bean设置lazyInit= false 属性，那么这个bean的依赖注入就会在容器初始化的时候完成



**上述步骤的结果是：XML Resource -> XML Document -> Bean Definition**



### 装载详解

loadBeanDefinitions ->  doLoadBeanDefinitions ->  doLoadDocument -> registerBeanDefinitions

1. loadBeanDefinitions

~~~java
	@Override
	public int loadBeanDefinitions(Resource resource) throws BeanDefinitionStoreException {
        //对Resource进行编码包装，确保读取内容的准确性
		return loadBeanDefinitions(new EncodedResource(resource));
	}
~~~



2. doLoadBeanDefinitions

~~~java
protected int doLoadBeanDefinitions(InputSource inputSource, Resource resource)
			throws BeanDefinitionStoreException {

		try {
            //根据XML文档获取document实例
			Document doc = doLoadDocument(inputSource, resource);
            //根据获取的document实例，来注册bean信息
			int count = registerBeanDefinitions(doc, resource);
			if (logger.isDebugEnabled()) {
				logger.debug("Loaded " + count + " bean definitions from " + resource);
			}
			return count;
		}
		catch (BeanDefinitionStoreException ex) {
			throw ex;
		}
		catch (SAXParseException ex) {
			throw new XmlBeanDefinitionStoreException(resource.getDescription(),
					"Line " + ex.getLineNumber() + " in XML document from " + resource + " is invalid", ex);
		}
		catch (SAXException ex) {
			throw new XmlBeanDefinitionStoreException(resource.getDescription(),
					"XML document from " + resource + " is invalid", ex);
		}
		catch (ParserConfigurationException ex) {
			throw new BeanDefinitionStoreException(resource.getDescription(),
					"Parser configuration exception parsing XML from " + resource, ex);
		}
		catch (IOException ex) {
			throw new BeanDefinitionStoreException(resource.getDescription(),
					"IOException parsing XML document from " + resource, ex);
		}
		catch (Throwable ex) {
			throw new BeanDefinitionStoreException(resource.getDescription(),
					"Unexpected exception parsing XML document from " + resource, ex);
		}
	}
~~~



3. doLoadDocument

~~~java
	protected Document doLoadDocument(InputSource inputSource, Resource resource) throws Exception {
        //获取XML Document 实例
		return this.documentLoader.loadDocument(inputSource, getEntityResolver(), 
        //getValidationModeForResource(resource) 获取指定资源（xml）的验证模型                       this.errorHandler,getValidationModeForResource(resource), isNamespaceAware());
	}
~~~





