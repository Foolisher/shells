


# AOP
它能做什么：**事务**、**安全**、**日志**、**缓存**、

## Aspect
方面定义，方面里面包含了对植入点，植入逻辑的定义

## Pointcut
对方法的匹配，在Spring中一般用表达式表达是表示 `[return type] [class and method name]([args])`

## Joinpoint

## Advice

### BeforeAdivce

### AfterAdivce

### AroundAdvice


## 植入原理


### 表达式

### 代理
代理分为动态代理和静态代理，动态代理指的是jdk动态代理，但jdk动态代理必须是基于接口的，利用了jdk动态生成内存字节码代理类的方式；


