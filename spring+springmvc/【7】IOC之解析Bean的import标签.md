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



1. import示例

如果项目工程较大，spring的配置文件全部写在一个spring.xml文件中，就会显的非常臃肿，难以维护。针对这种情况，spring提供了一个分模块的思路，利用 **import** 标签，例如我们可以构造这样一个spring.xml文件：

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="spring-student.xml"/>

    <import resource="spring-student-dtd.xml"/>

</beans>
~~~

spring.xml中，使用 **import** 标签的方式导入其他模块的配置文件。



2. importBeanDefinitionResource

spring中使用 **importBeanDefinitionResource(Element ele)** 完成对 **import** 标签的解析



~~~java
	protected void importBeanDefinitionResource(Element ele) {
        //1.获取 resource 的属性值
		String location = ele.getAttribute(RESOURCE_ATTRIBUTE);
		if (!StringUtils.hasText(location)) {
			getReaderContext().error("Resource location must not be empty", ele);
			return;
		}

		// Resolve system properties: e.g. "${user.dir}"
        // 2.解析系统属性， 如果 ${user.dir}
		location = getReaderContext().getEnvironment().resolveRequiredPlaceholders(location);
		
        //实际 Resource集合，即 import地址，有哪些Resource资源
		Set<Resource> actualResources = new LinkedHashSet<>(4);

		// Discover whether the location is an absolute or relative URI
        // 3.判断location是相对路径还是绝对路径
		boolean absoluteLocation = false;
		try {
			absoluteLocation = ResourcePatternUtils.isUrl(location) || ResourceUtils.toURI(location).isAbsolute();
		}
		catch (URISyntaxException ex) {
			// cannot convert to an URI, considering the location relative
			// unless it is the well-known Spring prefix "classpath*:"
		}

		// Absolute or relative?
        //4.绝对路径
		if (absoluteLocation) {
			try {
				int importCount = getReaderContext().getReader().loadBeanDefinitions(location, actualResources);
				if (logger.isTraceEnabled()) {
					logger.trace("Imported " + importCount + " bean definitions from URL location [" + location + "]");
				}
			}
			catch (BeanDefinitionStoreException ex) {
				getReaderContext().error(
						"Failed to import bean definitions from URL location [" + location + "]", ele, ex);
			}
		}
        //5.相对路径
		else {
			// No URL -> considering resource location as relative to the current file.
			try {
				int importCount;
				Resource relativeResource = getReaderContext().getResource().createRelative(location);
				if (relativeResource.exists()) {
					importCount = getReaderContext().getReader().loadBeanDefinitions(relativeResource);
					actualResources.add(relativeResource);
				}
				else {
					String baseLocation = getReaderContext().getResource().getURL().toString();
					importCount = getReaderContext().getReader().loadBeanDefinitions(
							StringUtils.applyRelativePath(baseLocation, location), actualResources);
				}
				if (logger.isTraceEnabled()) {
					logger.trace("Imported " + importCount + " bean definitions from relative location [" + location + "]");
				}
			}
			catch (IOException ex) {
				getReaderContext().error("Failed to resolve current resource location", ele, ex);
			}
			catch (BeanDefinitionStoreException ex) {
				getReaderContext().error(
						"Failed to import bean definitions from relative location [" + location + "]", ele, ex);
			}
		}
        // 6.解析成功后，进行监听器激活处理
		Resource[] actResArray = actualResources.toArray(new Resource[0]);
		getReaderContext().fireImportProcessed(location, actResArray, extractSource(ele));
	}
~~~



- 判断location是绝对路径还是相对路径

~~~java
absoluteLocation = ResourcePatternUtils.isUrl(location) || ResourceUtils.toURI(location).isAbsolute();
~~~

​	以 **classpath*:** 或者 **classpath:** 开头的为绝对路径

​	能够通过该location构建出**java.net.URL** 为绝对路径

​	根据location构建出 java.net.URI 判断调用 **#isAbsolute()** 方法



- 处理绝对路径

~~~java
public int loadBeanDefinitions(String location, @Nullable Set<Resource> actualResources) throws BeanDefinitionStoreException {
    //获取ResourceLoader对象
		ResourceLoader resourceLoader = getResourceLoader();
		if (resourceLoader == null) {
			throw new BeanDefinitionStoreException(
					"Cannot load bean definitions from location [" + location + "]: no ResourceLoader available");
		}

		if (resourceLoader instanceof ResourcePatternResolver) {
			// Resource pattern matching available.
			try {
				Resource[] resources = ((ResourcePatternResolver) resourceLoader).getResources(location);
                //递归处理
				int count = loadBeanDefinitions(resources);
				if (actualResources != null) {
					Collections.addAll(actualResources, resources);
				}
				if (logger.isTraceEnabled()) {
					logger.trace("Loaded " + count + " bean definitions from location pattern [" + location + "]");
				}
				return count;
			}
			catch (IOException ex) {
				throw new BeanDefinitionStoreException(
						"Could not resolve bean definition resource pattern [" + location + "]", ex);
			}
		}
		else {
			// Can only load single resources by absolute URL.
			Resource resource = resourceLoader.getResource(location);
			int count = loadBeanDefinitions(resource);
			if (actualResources != null) {
                //递归处理
				actualResources.add(resource);
			}
			if (logger.isTraceEnabled()) {
				logger.trace("Loaded " + count + " bean definitions from location [" + location + "]");
			}
			return count;
		}
	}
~~~



- 处理相对路径

如果location是相对路径，则会调用响应的Resource计算出响应的相对路径的Resource对象，然后判断：

​	如果该Resource存在，则调用  XmlBeanDefinitionReader#loadBeanDefinitions() 方法，进行BeanDefinition加载

​	如果不存在，则构造一个绝对的location，并调用loadBeanDefinitions() 方法，与绝对路径一样



3. 整个过程总结如下：
   1. 获取source属性值，得到正确的资源路径
   2. 调用 **XmlBeanDefinitionReader#loadBeanDefinitions(Resource... resources)** 方法，进行递归的 **BeanDefinition** 加载。

