## 资源文件加载路径问题

> 为什么加载WEB-INF下资源文件会去加载src/main/webapp/WEB-INF/目录下的文件而不是去 target/exploded/app.war/WEB-INF/里的资源文件？

`AbstractBeanDefinitionReader\(line:216\) resourceLoader.loadResource\("/WEB-INF/webx.xml"\);`









