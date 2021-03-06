# AOP

它能做什么：**事务**、**安全**、**日志**、**缓存**.

以前都是OOP的思想下编程模型，一切都是面向对象，显得井然有序，但某一天发现许多对象逻辑里有相同的代码逻辑或片段，比如记录日志、用户权限验证，他们都是做同样的事，这样，第一是觉得这样的代码对于有洁癖的人看起来是很反感的，再者若这些代码涉及改动时所有用到的点都要改。但又发现这些需要植入的地方只是一些关键地方。

那么，是否有一种方法使这些方法执行前或后再做点什么呢。方法是有的，目前比如有直接字节码操作、代理技术等来实现，字节码技术就是在编译时通过修改字节码的方式将需要植入的逻辑编译进去，只是我们编写时无需编写这些逻辑，这样的编译植入过程交由一个编译工具去做。代理技术与直接字节码操作技术的不同点在于它会另外生成一个代理类对目标类进行代理，然后在方法前或后加入希望的逻辑

## Aspect

方面定义，方面里面包含了对植入点，植入逻辑的定义。Aspect是一个切面的定义，一个切面里面定义了切入点（JoinPoint）、切入方式（PointCut）、切入点的逻辑（Advice），一个@Aspect类被解析成一个

## Advisor

PointCut和Advice的holder，PointCut是用来组织切面的，Advice是用来在切面上执行动作的，Advice有多种动作方式如前置、后置、前后置处理等方式

## Pointcut

对方法的匹配，在Spring中一般用表达式表达是表示 `[return type] [class and method name]([args])`，匹配表达式指定了哪些地方会被植入切面逻辑

## Joinpoint

具体的逻辑点

## Advice

是对具体植入动作行为的定义，也包括拦截的方式

通知，这里就是定义要植入逻辑的地方，植入也按执行时间分为许多种

1. **BeforeAdivce**

2. **AfterAdivce**

3. **Around**

4. **ThrowAdvice**

## 植入原理

1. 初始化BeanDefinition
2. createBean
3. initBean
4. find Advisors
5. match JoinPoint
6. create Proxy

**找到Advisors**

```java
BeanFactoryAdvisorRetrivelHelper.findAdvisorBeans()
```

### AopUtils

```java
// 找到匹配的切面
findAdvisorsThatCanApply(advisors, target);

// 判断 beanClass 是否可以被这个切面代理
AopUtils.canApply(pointCut, beanClass);
    MethodMatcher.matches
    AnnotationMethodMatcher   // 注解标记的解析，有目标注解的
    AspectJExpressionPointcut // 表达式标记的切面匹配
```

### 表达式

Spring使用AspectJ定义的切面表达式，虽然使用了AspectJ的AOP规范，但并未使用AspectJ的实现，AspectJ是静态编译，有自己的DSL，使用者需要学习其AOP语言，但是学习成本并不大，只是一些对切入点的描述定义，详细编写语法可见AspectJ文档

### 代理

代理分为动态代理和静态代理，动态代理指的是jdk动态代理，但jdk动态代理必须是基于接口的，利用了jdk动态生成内存字节码代理类的方式；另一只就是CGLIB静态代理，这种代理适用于对任何类的代理，它是利用继承的能力，代理原有类的方法，并植入逻辑

![](/assets/spring-aop-create.png)

* 拦截器是怎么植入进去的
* 调用到被拦截对象时，是怎么找到拦截器的
* 拦截调用的顺序图



