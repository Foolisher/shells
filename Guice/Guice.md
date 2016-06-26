


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
Guice是一个更轻量的容器管理，DI框架，DI的目的是为了解耦依赖与实现，使模块化成为可能，有利于减少所依赖方变更带来的影响。同时动态的运行时注入使测试更加方便，因为我们可以在注入时Mock测试数据

## Why
1. **快**： 取决于它的去xml配置式应用启动，因此特别快，但这块也只是限于启动时，运行时并没有表现许多优势

2. **灵活**： 灵活的注入方式，与装配方式，全是注解式定义DI对象，更迎合了编程式体验需求；多种注入方式给用户许多不同场景的注入需求

3. **简单**：简单，学习成本低。在注入定义，binding，甚至aop上，提供了很简介的api模型，使用简单，有人说注解加深了代码的侵入，不过Spring也在后来的版本中大力加入注解模式，至于说侵入，若没有带来不利影响，我们也无法批判好坏，简单，还易维护就是好的

4. **模块化**：Guice有更简洁的模块化开发体验，用编程模式组织模块是相对于XML文件方式更受人喜爱的方式


## How

### 注入技术
> 注入技术有对Field注入，set方法注入，构造函数注入;绑定技术有提供者模式注入，命令式注入，编译器注入等常见注入方式，下面对这些常见的注入方式进行简介

#### 命令式注入
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

// (2).订单服务注入支付服务类
public Class OrderServiceImpl{
  private PayService prePayService;
  private PayService normalPayService
  @Inject
  public void setPrePay(@PrePay PayService payService){...}
  @Inject
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
  .annotatedWith(Names.named("JDBC URL"))   (1). 字符串注入必须按名注入
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
有点相当于Spring的 **@Bean** 注解，该注解用于方法上，容器在获取该类型的实例时不是通过直接实例化该类，而是通过这样的工厂方法来创建，他的好处是可以让我们自定义对象创建逻辑。但是它的限制是工厂方法必须定义在 `AbstractModule` 的实现类中，在方法上加上 `@Provides` 注解。


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
有时我们的类并不是完全基于接口编程的，它们或许并没有实现类，例如有些Manager类，但这些Manager类又是需要在容器中管理的，只需使用 `bind(..)` 方法即可
```
bind(MyConcreteClass.class);
bind(AnotherConcreteClass.class).in(Singleton.class);
```

#### 构造函数绑定
从编程模式来说，有的人认为这是最佳的绑定方式，不过，呵呵，我没怎么觉得


#### 硬绑定（Just-in-time Bindings）
这种绑定方式就像Spring中的 `@Service，@Component`指定了实现类，注入接口时就明确了绑定对象，当然这种模式的限制是注入对象是编译时就确认了，这种方式相对使用起来简洁些，但是在在接口上加@ImplementedBy注解并不推荐这样做，我们尽量是面向接口的同时还希望屏蔽具体实现原则


### Scop
Guice也可指定对象作用域的，通常指定方式有
```
(1).通过注解方式
@Singleton
public class InMemoryTransactionLog implements TransactionLog {
...

(2).通过初始化方式
bind(TransactionLog.class).to(InMemoryTransactionLog.class).in(Singleton.class);
```


### AOP
容器技术少不了IOC，当然也少不了AOP，Guice也有AOP的，Guice的实现原理是通过继承父类，但它的aop实现方式与标准相较于Spring的AOP是要简介不少的，比如按表达式的匹配植入，对通知的前后或环绕通知或对异常的通知
```
public class NotOnWeekendsModule extends AbstractModule {
  protected void configure() {
                               (1).匹配带有注解NotOnWeekends的方法
    bindInterceptor(Matchers.any(), Matchers.annotatedWith(NotOnWeekends.class), 
        new WeekendBlocker()); (2).指定对这个方法的拦截逻辑
  }
}
```

当然相对于重量级的IOC框架Spring来说，Guice还是欠缺不少的，比如 **切面事务支持**、**ORM**、**Context Profile**、**表达式注入**、**缓存集成**、**MVC**，但作为一个轻量、快速的IOC框架来说，它有有其一席之地的，比如 **去配置化**、**模块化**、**效率高** 等特点，在实际项目中可酌情进行技术选型，其实我们项目中用到最多的就是 **IOC** + **AOP** 了，而Guice已具有这样的功能，是能满足我们大多数的项目需求的，同时Guice也可借助一些其他插件来实现这些功能的，只是并没有天然集成在Spring中而已



## web实战

### web.xml 配置
```
<filter>  
  <filter-name>guiceFilter</filter-name>    (1).用于配置请求控制器，与Jersey有相似的用法，通过Filter来拦截所有的请求，然后框架自己决定交给谁来处理
  <filter-class>com.google.inject.servlet.GuiceFilter</filter-class>
</filter>
<filter-mapping>  
  <filter-name>guiceFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>

<listener>                                  (2).用于容器初始化，相当于Spring的容器启动上下文listener
  <listener-class>org.packt.web.listener.FlightServletContextListener</listener-class></listener>

```


### 初始化容器
```
public class FlightServletContextListener extends GuiceServletContextListener {
	@Override
	protected Injector getInjector() {
		return Guice.createInjector(new ServletModule() {
			@Override
			protected void configureServlets() {
				install(new MainModule());                               (1).安装初始化我们的业务模块
				serve("/").with(IndexServlet.class);                     (2).url mapping 首页
				serve("/user.*").with(UserServlet.class);                (3).url mapping 用户相关
                serveRegex("^response").with(FlightServlet.class);       (4).正则式url mapping
                filter("/response").through(FlightSearchFilter.class);   (5).定义Filter
                
                bind(Filter.class).                                      (6).绑定容器bean
                  annotatedWith(FlightFilter.class).
                  to(FlightSearchFilter.class).in(Singleton.class);

			}
		});
	}
}
```
然后部署web服务器，就ok了，就这么简单










