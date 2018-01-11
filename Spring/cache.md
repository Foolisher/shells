## 原理

通过注解定义缓存的读写代理，如缓存获取代理，缓存失效关联，缓存加载代理

### 初始化流程

以一个用户服务为例：UserBean，里面有用户查询（缓存切面），用户修改（缓存失效）等逻辑

* 创建一个查询User\_BeanDefinition
* 初始化UserBean
* 在beanPostInit方法中执行Advisor匹配
* 如果它上面有Cache相关注解（由BeanFactoryCacheOperationSourceAdvisor发现）
* 把UserBean包装成被CacheInterceptor代理的
* 查询用户时，缓存代理先从缓存中查询，如果没有值则先从db中查询并放入缓存，然后返回结果



![](/assets/spring/spring-cache-init.png)

## 注解

### @Cache

## 拦截器

### AOP

### Interceptor



