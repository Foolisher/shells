## 为什么需要单元测试

1. 谁都不敢保证自己的code没有BUG，但可以通过严格的单测减少BUG

2. 尽早地发现错误，快速报错，遏制错误放大与辐射。因为错误放大后，对发现错误，修改错误，稳定性保障上的困难呈倍数增长

3. 可测试性，还驱动着设计合理，驱动着可读性，驱动着可维护性

  > 为保障自己的测试单元是稳定的，在设计时就会驱动自己设计出较高度抽象的模块，应当尽量不被修改破坏\( **开-闭原则** \)；
  > 
  > 因为职责越是单一明确越是便于测试，因此被测试单元应该是独立的行为能力\( **单一职责原则** \)
  > 
  > 为了测试简单可行，对外部系统的依赖应该是更小的\( **低耦合原则** \)，应当是尽量依赖接口而不是具体实现\( **依赖倒置原则** \)，这样通过Mock等技术在单测环境下才能使不依赖外部系统得以运行
  > 
  > 当自己要测试超过100行的方法，就算自己写的或许连自己都会看得眼花缭乱，还如何测试，更别提各种if\/else的分支覆盖率了，此时测试的可能性就会驱动你去重构，去对复杂逻辑进行拆分，对僵硬的判断逻辑进行抽象或重组。这样一来我们发现代码逻辑更清晰了，更易读了
  > 
  > 在设计合理上，代码可读性上有了一定的保证，才能让接盘的同学容易理解你的代码，可以放心安全的修改你的代码，修改高耦合代码带来的连带问题也是屡见不鲜的

4. 通过日常、预发环境测试，测试成本大，效率低，发现问题迟，修改问题的成本也大，而且那时修改问题的风险也大，从效率上来讲单测会提高我们的整体效率的！


## 有效的单元测试

1. 快速的

  > 我们的单测逻辑应当是快速易写的，我们的单测运行报告应当是快速运行完成产出的，这样才使我们有写单测的动力

2. 多边界输入，覆盖程度高

  > 单测最重要的意义不是对正确输入的验证，而是对单元异常输入是如何处理的考验，因为测试更喜欢对异常数据的测试，若测试向你报告一个BUG，或线上用户向你报告BUG后，你从问题排查，定位，修复到发布甚至回归或许会经历几个小时，但若在单测时多花10分钟就对这些异常覆盖到并考虑了处理逻辑，如此相比哪种方式更节省时间已经不用说了

3. 有断言，有意义逻辑校验

  > 常看到许多用main函数写的测试用例，许多用System.out.println出结果的测试用例，那么当哪天逻辑修改了，但单测并不会报错，而此时这个打印出的值或许已经不对了，你却不知道；或别人接手了跑这个单测时并不知道你当初要校验什么样的业务逻辑，只是看到有东西打印出来了。
  > 
  > 这并不算单测，顶多算一次多余的编译通过。有效的单测应当是有完整的逻辑校验\( **Assert** \)的，对输入输出有明确的预期的，这才是我们测试的目的


## 单元测试选型

![](http://fuxiao.oss-cn-shanghai.aliyuncs.com/book/test-framework.png)

前面安利了咱为何需要单测，那么接下来就说说单测的一些利器

Java coder无人不知JUnit，但它只是个测试环境，提供了一些断言工具，而且在测试功能上较TestNG弱些，例如对一个方法在多条件输入情况下，JUnit需重复执行多次，set多次参数，而TestNG可以通过对多次输入的参数进行配置并对结果断言；

为让我们的测试单元独立可运行，但我们的系统又常常会依赖一些外部jar包甚至RPC系统，通常无法或不希望实际依赖他们的服务对我们的单元进行测试，那么像诸如JMock、EasyMock、Mockito等代理型的Mock框架出现了，它们可以模拟出依赖的服务，并设定我们期望的返回结果使得我们的测试可以进行下去，但JMock、EasyMock等由于由于实现技术问题对例如静态方法模拟，静态域模拟支持不太好，大多数情况会结合PowerMock来使用，其实有个工具 **JMockit** 可以完全替代它们的，它们合并起来的功能JMockit都有。

但即使用到JMockit也有许多问题，例如JUnit+Mock方式，对于同一方法多条件输入的校验会设置多次参数并执行多次，代码大量增长，当然可以通过TestNG来实现配置化多参输入，但对它的XML配置方式来做个人觉得更繁琐，而且目前那些mock框架的断言都是doReturn\(userInfo\).when\(userDao\).getUserInfo\(anyLong\(\)\);这样的模式，可读性很差，代码量大，本身java就是一个啰嗦的语言，测试过程中没有明确的划分预知条件、执行环境、期望结果等阶段，若代码可读性不是很好的话看起来会很乱

安利了一堆单测的重要性和方法论，那么接下来再推一个炫酷的单测框架 **Spock** ：

## Spock

它可以运行在JUnit上，还拥有TestNG的参数化测试特性，却没有它那么繁琐\(xml配置化\)，它提高了一种表格化多参输入；它能做到其他任何java mock框架能做到的mock，而且运用groovy的语法特性使代码更加简洁干练；对代码质量，对测试用例编写效率，对代码有洁癖追求的你值得拥有！

Spock是运用于Java\/Groovy语言编写的项目中一种规格表述式的测试框架，规格化模型来源于一种编程思想，它认为程序在流程阶段上是可描述的，例如对前置条件的设定描述，对执行流程的描述，对预期结果的描述。Spock之所以能在众多单测开中脱颖而出得益于groovy的漂亮语法与表述性测试用例的编程模式，将执行与预测分离。它依然利用的是JUnit执行器，可平滑地集成到常见主流的IDE中，无缝接入已有的持续集成服务，当然它的成功也脱离不了一些优秀测试框架，测试思想，编程语言提供的灵感

### 实践

```groovy

import spock.lang.Specification
import spock.lang.Unroll
/** 
  * 数据集规则绑定测试用例 
  * 测试需求: 
  * 1. 验证数据集不能为空 
  * 2. 验证规则id不能为空 
  * 3. 数据类型不能为空且数据合法 
  * 4. 数据来源不能为空且数据需合法 
  * 5. 为避免重复bind,需校验是否重复绑定,然后给出提示 
  * 6. 对正常数据进行测试,并且保证是正确的 
  * Created by 付笑 on 2016-06-15. 
  */

class DataCollectionRuleServiceImplTest extends Specification {

 // service 服务中用到的 Dao,因为依赖接口,所以需要对其mock,并对方法进行mock 
 def dataCollectionRuleDAO = Mock(TdeDataCollectionRuleDAO) 
// 待测试的 Service 服务 
 def ruleService = new DataCollectionRuleServiceImpl( dataCollectionRuleDAO: dataCollectionRuleDAO)

 // 测试绑定规则服务 
@Unroll def "test bindRule"() {

 given: // 1. 初始化一个输入Entity 
 def collRule = new TdeDataCollectionRuleDO( 
           collectionId: collectionId, // 数据集id required 
           ruleId: ruleId, // 数据规则id required 
           dataType: dataType, // 数据类型 in (1,2,3) 
           dataSrc: dataSrc // 数据来源 in (1,2,3) );

 when: 
  // 对数据查询接口进行Mock,应为Service中用到了DAO服务,为让流程继续 
  // 走下去,需要数据链路是通的,并且能按自己的意愿在不同的场景返回不同的结果 
  // groovy 在lambda表达式的语法下构建一个方法Mock显得如此优雅: 
 dataCollectionRuleDAO.getListByCondition(_) >> { param -> 
  // 若用户输入数据集id==1, 规则id=="1" 就模拟数据库中存在该条记录,表示数据 
  // 重复,不可再绑定的业务逻辑 ，对不同的输入Mock不同的返回结果
   if (param[0].getCollectionId() == 1l && param[0].getRuleId() == "1") 
     Lists.newArrayList(collRule); 
   else 
     Lists.newArrayList(); 
 } 

 // 执行我们需要测试的服务逻辑 
 def rst1 = ruleService.bindRule(collRule)

 then: // 对返回结果进行断言 
 rst1.message == msg rst1.success == success

 where: // 针对任意的输入边界进行测试,对不同的输入/删除进行预测,看输出在设定的输入下是否符合预期结果 
 collectionId | ruleId | dataType | dataSrc | success | msg 
 null         | "1"    | 1        | 1       | false   | 'collection.id.null' 
 1            | null   | 1        | 1       | false   | 'rule.id.null' 
 1            | "2"    | -1       | 1       | false   | 'error.dataType' 
 1            | "1"    | 1        | 1       | false   | '重复的数据集和规则' 
 1            | "2"    | 1        | 1       | true    | null

}}


```

据以往用java mock框架来写这样规格的测试用例是需要大篇幅代码的，不管是方法Mock，边界测试，断言预测等过程，而且java的代码一旦代码多起来就开始凌乱了。

下面将对使用Spock的各种其他的测试姿势进行介绍：

#### 数据表格化的参数

适用于边界测试，对同一个方法的不同输入进行测试，并能对其结果断言

```groovy
expect:
 Math.max(a, b) == c
where:
 a | b | c
 1 | 3 | 3 //passes
 7 | 4 | 4 //fails
 0 | 0 | 0 //passes
```

#### 数据管道

有点类似yeild功能，它是表格数据迭代存取的另一种方式，对管道数据用一次，将会跳到下一个值，它对多场景输入提供了便利，它较之于表格模式的灵活性处在于它可以让输入参数是可动态获取的\(来自于数据库、文件、网络...\)，而不是写死的

```groovy
// 这样定义管道数据时，用到a/b/c参数的测试方法将被调用3次，并且结果一次是列表中的顺序值
where:
a << [3, 7, 0]
b << [5, 0, 0]
c << [5, 7, 0]
```

#### 调用次数预测

我们经常会对测试流程中涉及的某些重要方法调用次数进行验证，验证是否调用过，或调用次数是否是自己预期的，被调用的参数是否符合当前场景等

```groovy
1 * subscriber.receive("hello") // exactly one call
0 * subscriber.receive("hello") // zero calls
(1..3) * subscriber.receive("hello") // between one and three calls (inclusive)
(1.._) * subscriber.receive("hello") // at least one call
(_..3) * subscriber.receive("hello") // at most three calls
_ * subscriber.receive("hello") // any number of calls, including zero
```

### DSL

```groovy
  given: //data initialization goes here (includes creating mocks)
   when: //invoke your test subject here and assign it to a variable
   then: //assert data here
cleanup: //optional
  where: //optional:provide parametrized data (tables or pipes)
```

### 添加依赖

```xml
            <!-- Spock Framework basic dependencies: -->
            <dependency>
                <groupId>org.codehaus.groovy</groupId>
                <artifactId>groovy-all</artifactId>
                <version>2.4.3</version>
                <scope>test</scope>
            </dependency>
            <!-- The version have to be compatible with Groovy -->
            <dependency>
                <groupId>org.spockframework</groupId>
                <artifactId>spock-core</artifactId>
                <version>1.0-groovy-2.4</version>
                <scope>test</scope>
            </dependency>

            <!--To use Hamcrest matchers:-->
            <dependency>
                <groupId>org.hamcrest</groupId>
                <artifactId>hamcrest-core</artifactId>
                <version>1.3</version>
                <scope>test</scope>
            </dependency>
            <!-- To mock classes: -->
            <dependency>
                <groupId>cglib</groupId>
                <artifactId>cglib-nodep</artifactId>
                <version>3.1</version>
                <scope>test</scope>
            </dependency>
            <!-- Use with cglib to mock classes without default constructor: -->
            <dependency>
                <groupId>org.objenesis</groupId>
                <artifactId>objenesis</artifactId>
                <version>2.1</version>
                <scope>test</scope>
            </dependency>
```

以上示例只是对Spock的简单介绍，更详细的文档可以参考Spock官网

References:

* [http:\/\/www.atatech.org\/articles\/55655](http://www.atatech.org/articles/55655)

* [http:\/\/spockframework.github.io\/spock\/docs\/1.1-rc-1\/index.html](http://spockframework.github.io/spock/docs/1.1-rc-1/index.html)


