


# Guice
开门见山，直接上Getting Started 
```
class BillingService { //                              (1).服务逻辑类
  private final CreditCardProcessor processor; //      
  private final TransactionLog transactionLog;

  @Inject                                              (2).声明注入，表明这里是按构造函数注入
  BillingService(CreditCardProcessor processor, 
      TransactionLog transactionLog) {
    this.processor = processor;
    this.transactionLog = transactionLog;
  }

  public Receipt chargeOrder(PizzaOrder order, CreditCard creditCard) {
    ...
  }
}

public class BillingModule extends AbstractModule {
  @Override 
  protected void configure() {                        (3).绑定接口的实现类，以便在注入的地方带入该实现类
    bind(TransactionLog.class).to(DatabaseTransactionLog.class);
    bind(CreditCardProcessor.class).to(PaypalCreditCardProcessor.class);
  }
}

public static void main(String[] args) {              (4).创建某Module的注入器
    Injector injector = Guice.createInjector(new BillingModule());
                                                      (5).获取实例对象
    BillingService billingService = injector.getInstance(BillingService.class);
    ...
  }

```


## What
Guice是一个更轻量的容器管理，DI框架，DI的目的是为了解耦依赖于实现，是模块化成为可能，有利于减少所依赖方变更带来的影响。同时动态的运行时注入是测试更加方便，因为我们可以在注入时Mock测试数据

## Why


## How

### 注入技术

#### 普通注入
普通注入是最直接的注入，直接绑定了接口的实现类，在容器进行注入时按绑定关系进行注入
```
// 当出现注入TransactionLog的地方，皆使用实现类DatabaseTransactionLog
bind(TransactionLog.class).to(DatabaseTransactionLog.class);
```

#### 注解式注入

#### 按名称注入

#### 实例化注入
  实例化注入与其他注入方式的不同在于实际被绑定对象不是有容器创建的，而是在定义注入逻辑时由用户自己实例化被注入对象，例如：
```
bind(String.class)
  .annotatedWith(Names.named("JDBC URL"))
  .toInstance("jdbc:mysql://localhost/pizza");
// 在测试时我们希望为标注了测试用户的注入地方自定义实例，用法如下
bind(User.class)
  .annotatedWith(Names.named("MockedUser"))
  .toInstanceWIth(new User(){
    {
      this.name = "I am ET";
    }
  });
```

#### 对象工厂方法式
有点相当于Spring的 **@Bean** 注解，该注解用于方法上，











