### 解析import标签

在注册BeanDefinitions中，DefaultBeanDefinitionDocumentReader#doRegisterBeanDefinitions(Element root)方法中parseBeanDefinitions()，Spring 有两种解析Bean的方式：

如果根节点和子节点采用默认名称空间的话，则调用 **parseDefaultElement**(Element ele, BeanDefinitionParserDelegate delegate) 方法，如果采用的是自定义名称空间，则调用 **parseCustomElement**(Element ele) 方法



- 采用默认名称空间

~~~java
private void parseDefaultElement(Element ele, BeanDefinitionParserDelegate delegate) {
    	//解析 import 标签
		if (delegate.nodeNameEquals(ele, IMPORT_ELEMENT)) {
			importBeanDefinitionResource(ele);
		}
    	// 解析 alias 标签
		else if (delegate.nodeNameEquals(ele, ALIAS_ELEMENT)) {
			processAliasRegistration(ele);
		}
    	// 解析 bean 标签
		else if (delegate.nodeNameEquals(ele, BEAN_ELEMENT)) {
			processBeanDefinition(ele, delegate);
		}
    	// 解析 beans 标签
		else if (delegate.nodeNameEquals(ele, NESTED_BEANS_ELEMENT)) {
			// recurse
			doRegisterBeanDefinitions(ele);
			doRegisterBeanDefinitions(ele);
		}
	}
~~~

