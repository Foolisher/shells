## 资源文件加载路径问题

> 为什么加载WEB-INF下资源文件会去加载src/main/webapp/WEB-INF/目录下的文件而不是去 target/exploded/app.war/WEB-INF/里的资源文件？

`AbstractBeanDefinitionReader\(line:216\) resourceLoader.loadResource\("/WEB-INF/webx.xml"\);`

debug发现：

```
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



发现ServletContext路径居然是从 src/main/webapp/ 开始的，这就奇怪了，













































