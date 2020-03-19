在 xmlBeanDefinitionReader#doLoadDocument(InputSource inputSource,Resource resource) 方法中,做了两件事：

1. ```java
   //获取验证模式
   getValidationModeForResource(resource)
   ```

2. ```
   获取 XML Document 实例
   this.documentLoader.loadDocument(inputSource, getEntityResolver(), this.errorHandler,
   				getValidationModeForResource(resource), isNamespaceAware());
   ```



1.  DocumentLoader：定义从资源文件加载到转化为Document的功能

~~~java
public interface DocumentLoader {
	Document loadDocument(
			InputSource inputSource, EntityResolver entityResolver,
			ErrorHandler errorHandler, int validationMode, boolean namespaceAware)
			throws Exception;

}
~~~

- inputSource：加载Document的资源
- entityResolver：解析文件的解析器
- errorHandler：处理加载Document对象过程中的错误
- validationMode：验证模式
- namespaceAware：名称空间支持



2. DefaultDocumentLoader：是DocumentLoader的默认实现类

**loadDocument ->  createDocumentBuilderFactory -> createDocumentBuilder -> builder.parse**



3.  EntityResolver ： 通过实现它，应用可以自定义如何**寻找**【验证文件】的逻辑。 



> 在 loadDocument 方法中涉及一个参数 EntityResolver ，何为EntityResolver？官网这样解释：如果 SAX 应用程序需要实现自定义处理外部实体，则必须实现此接口并使用 setEntityResolver 方法向SAX 驱动器注册一个实例。也就是说，对于解析一个XML，SAX 首先读取该 XML 文档上的声明，根据声明去寻找相应的 DTD 定义，以便对文档进行一个验证。默认的寻找规则，即通过网络（实现上就是声明的DTD的URI地址）来下载相应的DTD声明，并进行认证。下载的过程是一个漫长的过程，而且当网络中断或不可用时，这里会报错，就是因为相应的DTD声明没有被找到的原因。
>
> EntityResolver 的作用是项目本身就可以提供一个如何寻找 DTD 声明的方法，即由程序来实现寻找 DTD 声明的过程，比如我们将 DTD 文件放到项目中某处，在实现时直接将此文档读取并返回给 SAX 即可。这样就避免了通过网络来寻找相应的声明。

