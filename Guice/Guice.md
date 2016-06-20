


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
                                                      (5).获取容器中的实例对象
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
普通注入是最直接的注入，直接绑定了接口的实现类，在容器进行注入时按绑定关系进行注入，是最常用的绑定关系定义
```
// 当出现注入TransactionLog的地方，皆使用实现类DatabaseTransactionLog
bind(TransactionLog.class).to(DatabaseTransactionLog.class);
```

#### 注解式注入
用自定义注解注入可以区分自己特殊的注入类型，例如，我们在下单服务中定义类两个支付服务，一个是预售支付，一个是一次性支付，它们有不同的实现，但接口签名是一样的，我们怎么利用Guice来实现这样的注入呢，当然Spring里面很简单，可以按名注入，我们尝试Guice是怎样做的
```
// (1).首先得定义两个注解
@PrePay
@NormalPay

// (2).订单服务
public Class OrderServiceImpl{
  public void setPrePay(@PrePay PayService payService){...}
  public void setNormalPay(@NormalPay PayService payService){...}
}

// (3).绑定实现类
bind(PayService.class)
  .annotatedWith(PrePay.class)
  .to(PrePayService.class);
bind(PayService.class)
  .annotatedWith(NormalPay.class)
  .to(NormalPayService.class);

```

#### 按名称注入
按名称注入的实现目标与上述按注解注入是一样的，只是他们的方式不一样而已，若使用按名称注入的话上面的例子中就无需定义两个注解了，相对来说按名称注入较为简单一些，但从编码风格来说也有人更喜欢注解式注入，他们认为这样是代码看起来更严谨，容易通过IDE查找使用上下文，同时不容易出现编码带来的错误
```
bind(PayService.class)
  .annotatedWith(Names.named("PrePay"))
  .to(PrePayService.class);
bind(PayService.class)
  .annotatedWith(Names.named("NormalPay"))
  .to(NormalPayService.class);
```

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
有点相当于Spring的 **@Bean** 注解，该注解用于方法上，容器在获取该类型的实例时不是通过直接实例化该类，而是通过这样的工厂方法来创建，他的好处是可以让我们自定义对象创建逻辑。但是它的限制是工厂方法必须定义在 `AbstractModule` 的实现类中。


#### 注解式对象工厂方法
工厂式的注入有工厂方法式注入(上述提及)以及 **Provider** 模式的注入，当自定义工厂方法创建逻辑变得很复杂，而且多起来时，将这些创建逻辑都放在初始化类中是不可取的，这是另一种将这些逻辑分散的对象工厂模式出来了，它必须实现 `Provider` 接口的 `get()` 方法，以声明对象的创建逻辑。
```
public class DatabaseTransactionLogProvider implements Provider<TransactionLog> {
  public TransactionLog get() {  (1).声明创建TransactionLog具体实现类的工厂方法
    DatabaseTransactionLog transactionLog = new DatabaseTransactionLog();
    transactionLog.setConnection(...);
    ...
    return transactionLog;
  }
}

public class BillingModule extends AbstractModule {
  @Override
  protected void configure() {
    bind(TransactionLog.class) (2).将TransactionLog的实现类指定为由DatabaseTransactionLogProvider提供
        .toProvider(DatabaseTransactionLogProvider.class);
  }

```

#### “无目标”的绑定
有时我们的类并不是完全基于接口编程的，它们或许并没有实现类，例如有些Manager类，但这些Manager类又是需要在容器中管理的
```
bind(MyConcreteClass.class);
bind(AnotherConcreteClass.class).in(Singleton.class);
```












