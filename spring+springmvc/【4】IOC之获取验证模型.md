XML文件的验证模式主要保证了XML文件的正确性



### DTD与XSD

- DTD（Document Type Definition）：文档类型定义

  为 XML 文件的验证机制，属于 XML 文件中组成的一部分。DTD 是一种保证 XML 文档格式正确的有效验证方式，它定义了相关 XML 文档的元素、属性、排列方式、元素的内容类型以及元素的层次结构。其实 DTD 就相当于 XML 中的 “词汇”和“语法”，我们可以通过比较 XML 文件和 DTD 文件 来看文档是否符合规范，元素和标签使用是否正确。

- XSD（XML Schemas Definition）: XML Schema 本身就是一个 XML文档，使用的是 XML 语法，因此可以很方便的解析 XSD 文档。相对于 DTD，XSD 具有如下**优势** 

  - XML Schema 基于 XML ，没有专门的语法。
  - XML Schema 可以像其他 XML 文件一样解析和处理。
  - XML Schema 比 DTD 提供了更丰富的数据类型。
  - XML Schema 提供可扩充的数据模型。
  - XML Schema 支持综合命名空间。
  - XML Schema 支持属性组



### getValidationModeForResource

~~~java
protected int getValidationModeForResource(Resource resource) {
    //获取指定的验证模式
		int validationModeToUse = getValidationMode();
    //如果手动指定，则直接返回
		if (validationModeToUse != VALIDATION_AUTO) {
			return validationModeToUse;
		}
    //自动获取验证模式
		int detectedMode = detectValidationMode(resource);
		if (detectedMode != VALIDATION_AUTO) {
			return detectedMode;
		}
		// Hmm, we didn't get a clear indication... Let's assume XSD,
		// since apparently no DTD declaration has been found up until
		// detection stopped (before finding the document's root tag).
    //最后使用XSD作为默认
		return VALIDATION_XSD;
	}
~~~



~~~java
//自动获取验证模式
protected int detectValidationMode(Resource resource) {
		if (resource.isOpen()) {
			throw new BeanDefinitionStoreException(
					"Passed-in Resource [" + resource + "] contains an open stream: " +
					"cannot determine validation mode automatically. Either pass in a Resource " +
					"that is able to create fresh streams, or explicitly specify the validationMode " +
					"on your XmlBeanDefinitionReader instance.");
		}

		InputStream inputStream;
		try {
			inputStream = resource.getInputStream();
		}
		catch (IOException ex) {
			throw new BeanDefinitionStoreException(
					"Unable to determine validation mode for [" + resource + "]: cannot open InputStream. " +
					"Did you attempt to load directly from a SAX InputSource without specifying the " +
					"validationMode on your XmlBeanDefinitionReader instance?", ex);
		}

		try {
			return this.validationModeDetector.detectValidationMode(inputStream);
		}
		catch (IOException ex) {
			throw new BeanDefinitionStoreException("Unable to determine validation mode for [" +
					resource + "]: an error occurred whilst reading from the InputStream.", ex);
		}
	}
~~~

