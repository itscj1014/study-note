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



- createBeanDefinitionDocumentReader

~~~java
private Class<? extends BeanDefinitionDocumentReader> documentReaderClass =
			DefaultBeanDefinitionDocumentReader.class;

protected BeanDefinitionDocumentReader createBeanDefinitionDocumentReader() {
		return BeanUtils.instantiateClass(this.documentReaderClass);
	}
~~~



- registerBeanDefinitions

~~~java
public interface BeanDefinitionDocumentReader {

	/**
	 * Read bean definitions from the given DOM document and
	 * register them with the registry in the given reader context.
	 * @param doc the DOM document
	 * @param readerContext the current context of the reader
	 * (includes the target registry and the resource being parsed)
	 * @throws BeanDefinitionStoreException in case of parsing errors
	 */
	void registerBeanDefinitions(Document doc, XmlReaderContext readerContext)
			throws BeanDefinitionStoreException;

}
~~~



- DefaultBeanDefinitionDocumentReader

BeanDefinitionDocumentReader的默认实现类

~~~java
@Override
	public void registerBeanDefinitions(Document doc, XmlReaderContext readerContext) {
		this.readerContext = readerContext;
		doRegisterBeanDefinitions(doc.getDocumentElement());
	}
~~~



~~~java
protected void doRegisterBeanDefinitions(Element root) {
		
    	//1.记录老的BeanDefinitionParserDelegate对象
		BeanDefinitionParserDelegate parent = this.delegate;
    	//2.创建BeanDefinitionParserDelegate对象，并记录到delegate中
		this.delegate = createDelegate(getReaderContext(), root, parent);
		//3.检查</beans>根标签名称空间是否为空
		if (this.delegate.isDefaultNamespace(root)) {
            //4.处理profile属性
			String profileSpec = root.getAttribute(PROFILE_ATTRIBUTE);
			if (StringUtils.hasText(profileSpec)) {
                //5.使用分隔符切分，可能有多个profile
				String[] specifiedProfiles = StringUtils.tokenizeToStringArray(
						profileSpec, BeanDefinitionParserDelegate.MULTI_VALUE_ATTRIBUTE_DELIMITERS);
				
                //6.如果所有profile都无效，则不进行注册
				if (!getReaderContext().getEnvironment().acceptsProfiles(specifiedProfiles)) {
					if (logger.isDebugEnabled()) {
						logger.debug("Skipped XML bean definition file due to specified profiles [" + profileSpec +
								"] not matching: " + getReaderContext().getResource());
					}
					return;
				}
			}
		}
		//7.解析前处理
		preProcessXml(root);
    	//8.解析
		parseBeanDefinitions(root, this.delegate);
    	//9.解析后处理
		postProcessXml(root);
		//10.重新设置delegate为老的BeanDefinitionParserDelegate对象
		this.delegate = parent;
	}
~~~



1. 记录老的BeanDefinitionParserDelegate对象
2. 创建BeanDefinitionParserDelegate对象，BeanDefinitionParserDelegate对象负责解析BeanDefinition

~~~java
	protected BeanDefinitionParserDelegate createDelegate(
			XmlReaderContext readerContext, Element root, @Nullable BeanDefinitionParserDelegate parentDelegate) {

		BeanDefinitionParserDelegate delegate = new BeanDefinitionParserDelegate(readerContext);
		delegate.initDefaults(root, parentDelegate);
		return delegate;
	}
~~~

8. parseBeanDefinitions

~~~java
protected void parseBeanDefinitions(Element root, BeanDefinitionParserDelegate delegate) {
    	//1.如果根节点使用默认名称空间，执行默认解析
		if (delegate.isDefaultNamespace(root)) {
            //2.遍历子节点
			NodeList nl = root.getChildNodes();
			for (int i = 0; i < nl.getLength(); i++) {
				Node node = nl.item(i);
				if (node instanceof Element) {
					Element ele = (Element) node;
                    //3.使用默认名称空间，执行默认解析
					if (delegate.isDefaultNamespace(ele)) {
						parseDefaultElement(ele, delegate);
					}
					else {
                        //4.非默认名称空间，执行自定义解析
						delegate.parseCustomElement(ele);
					}
				}
			}
		}
		else {
            //5.如果根节点非默认名称空间，执行自定义解析
			delegate.parseCustomElement(root);
		}
	}
~~~



spring有两种bean申明方式：

- 配置文件式：对应着1处,执行默认解析

~~~java
private void parseDefaultElement(Element ele, BeanDefinitionParserDelegate delegate) {
		if (delegate.nodeNameEquals(ele, IMPORT_ELEMENT)) {
			importBeanDefinitionResource(ele);
		}
		else if (delegate.nodeNameEquals(ele, ALIAS_ELEMENT)) {
			processAliasRegistration(ele);
		}
		else if (delegate.nodeNameEquals(ele, BEAN_ELEMENT)) {
			processBeanDefinition(ele, delegate);
		}
		else if (delegate.nodeNameEquals(ele, NESTED_BEANS_ELEMENT)) {
			// recurse
			doRegisterBeanDefinitions(ele);
		}
	}
~~~



- 自定义注解式：对应着5处

~~~java
public BeanDefinition parseCustomElement(Element ele) {
		return parseCustomElement(ele, null);
	}
~~~



~~~java
	public BeanDefinition parseCustomElement(Element ele, @Nullable BeanDefinition containingBd) {
		String namespaceUri = getNamespaceURI(ele);
		if (namespaceUri == null) {
			return null;
		}
		NamespaceHandler handler = this.readerContext.getNamespaceHandlerResolver().resolve(namespaceUri);
		if (handler == null) {
			error("Unable to locate Spring NamespaceHandler for XML schema namespace [" + namespaceUri + "]", ele);
			return null;
		}
		return handler.parse(ele, new ParserContext(this.readerContext, this, containingBd));
	}
~~~



- createReaderContext：创建 XmlReaderContext 对象

~~~java
	public XmlReaderContext createReaderContext(Resource resource) {
		return new XmlReaderContext(resource, this.problemReporter, this.eventListener,
				this.sourceExtractor, this, getNamespaceHandlerResolver());
	}
~~~





![BeanDefinition的解析过程](https://raw.githubusercontent.com/itscj1014/PictureBed/master/img/effccer.jpg)