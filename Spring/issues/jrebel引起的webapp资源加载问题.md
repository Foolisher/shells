## 资源文件加载路径问题

> 为什么加载WEB-INF下资源文件会去加载src/main/webapp/WEB-INF/目录下的文件而不是去 target/exploded/app.war/WEB-INF/里的资源文件？

`AbstractBeanDefinitionReader(line:216) resourceLoader.loadResource("/WEB-INF/webx.xml");`

debug发现：

```java
"RMI TCP Connection(2)-127.0.0.1@2537" daemon prio=5 tid=0x2a nid=NA runnable
  java.lang.Thread.State: RUNNABLE
      at org.springframework.web.context.support.ServletContextResource.<init>(ServletContextResource.java:66)
      at org.springframework.web.context.support.AbstractRefreshableWebApplicationContext.getResourceByPath(AbstractRefreshableWebApplicationContext.java:174)
      at com.alibaba.citrus.springext.support.context.XmlWebApplicationContext.getResourceByPath(XmlWebApplicationContext.java:115)
      at org.springframework.core.io.DefaultResourceLoader.getResource(DefaultResourceLoader.java:92)
      at org.springframework.core.io.support.PathMatchingResourcePatternResolver.getResources(PathMatchingResourcePatternResolver.java:291)
      at com.alibaba.citrus.springext.support.context.XmlWebApplicationContext$1.getResources(XmlWebApplicationContext.java:138)
      at org.springframework.context.support.AbstractApplicationContext.getResources(AbstractApplicationContext.java:1269)
      at org.springframework.beans.factory.support.AbstractBeanDefinitionReader.loadBeanDefinitions(AbstractBeanDefinitionReader.java:216)
      at org.springframework.beans.factory.support.AbstractBeanDefinitionReader.loadBeanDefinitions(AbstractBeanDefinitionReader.java:188)
      at org.springframework.web.context.support.XmlWebApplicationContext.loadBeanDefinitions(XmlWebApplicationContext.java:125)
      at org.springframework.web.context.support.XmlWebApplicationContext.loadBeanDefinitions(XmlWebApplicationContext.java:94)
      at org.springframework.context.support.AbstractRefreshableApplicationContext.__refreshBeanFactory(AbstractRefreshableApplicationContext.java:129)
      at org.springframework.context.support.AbstractRefreshableApplicationContext.refreshBeanFactory(AbstractRefreshableApplicationContext.java:-1)
      at org.springframework.context.support.AbstractApplicationContext.obtainFreshBeanFactory(AbstractApplicationContext.java:609)
      at org.springframework.context.support.AbstractApplicationContext.__refresh(AbstractApplicationContext.java:510)
      - locked <0x340f> (a java.lang.Object)
```

![](/assets/spring/servlet-context.png)

发现ServletContext路径居然是从 src/main/webapp/ 开始的，这就奇怪了，为何不是 target/exploded/mpchoice.war/ 呢，这里才是解开后的路径啊。由于web里面有许多placeholder要在打包阶段替换，若去找工程文件路径的话，那就找不到了，是要找编译后的项目路径才能拿到被注入过变量的文件。那么问题是如何让这里的ServletContext识别正确的rootpath呢？

![](/assets/spring/jrebel-path.png)

这还需从jrebel的原理分析了，启用jrebel后所有的class、包括webapp的资源加载都由jrebel来做，那么对于webapp的web配置，jrebel由于无法知道web资源exploded路径，遂以项目路径作为资源加载路径，告诉ServletContext这就是webapp路径，那么久不对了，该问题的解决办法是修改jrebel里面的 / 路径指向为解压开的路径，包括 /WEB-INF

