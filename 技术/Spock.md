# 为什么需要单元测试

> 如果一个20行的函数刚写完时作者就能发现有BUG，找到并修复这个BUG可能只需要1分钟，并且不担心影响其它使用者。如果是在200行的一个类中，别人调用时发现有BUG，阅读代码并定位问题可能就需要一个小时，对这个问题的修复重新代码评审又要花一个小时。如果在系统和系统联调的时候才发现这个问题，前面扯皮就要半天，改完了重新回归又是半天。如果改这个BUG的人已经不是原作者的话，往往担心改了这个BUG又引入其它问题，于是修改方案就要拖一群人讨论半天，最终改完了要求QA作大范围的回归，结果还是还不放心。
> 
> 错误率恒定律告诉我们错误是不可避免的，而规模代价平方律告诉我们要尽早发现错误。单元测试的目的是尽早在尽量小的范围内暴露错误

1. 尽早地发现错误，快速报错，遏制错误放大与辐射。错误放大，对发现错误，修改错误，保障稳定性更加困难

2. 可测试，驱动设计合理，驱动可读性，驱动可维护性
3. 谁都不能说自己的code没有BUG，但可以通过严格的单测减少BUG


# 有效的单元测试

1. 快速的

2. 多边界输入

3. 有断言

# 单元测试选型

# Spock

Spock是一个基于Java与Groovy语言的测试框架，借助于Groovy优雅的表达式语言与独具一格的测试模式使它脱颖而出，当然他的发展也是站在JUnit、JMock、Groovy等巨人的肩膀上的

## 为什么选择Spock

## 单元测试

### TDD

## JUnit {#d}

## Spock VS JUnit+JMockit

## 实践

#### 数据表格

#### 数据管道

### DSL

#### expect

相当于JUnit里面的断言，这里是我们单测期望的结果，

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

### 数据集规则管理

**Spock 版本**

```groovy

/**
 * 数据集规则测试用例
 * Created by wanggen/付笑 on 2016-06-15.
 */
class DataCollectionRuleServiceImplTest extends Specification {

    def dataCollectionRuleDAO = Mock(TdeDataCollectionRuleDAO)
    def ruleService = new DataCollectionRuleServiceImpl(dataCollectionRuleDAO: dataCollectionRuleDAO)

    def "test bindRule"() {
        when:
        def rst1 = ruleService.bindRule(new TdeDataCollectionRuleDO())
        def rst2 = ruleService.bindRule(new TdeDataCollectionRuleDO(collectionId: 1, ruleId: 1))

        dataCollectionRuleDAO.getListByCondition(_) >> newArrayList(new TdeDataCollectionRuleDO()) >> newArrayList()
        def rst3 = ruleService.bindRule(new TdeDataCollectionRuleDO(collectionId: 1, ruleId: 1, dataType: 1))
        def rst4 = ruleService.bindRule(new TdeDataCollectionRuleDO(collectionId: 1, ruleId: 2, dataType: 1))
        then:
        rst1.message == 'collection.id.null'
        rst2.message == 'error.dataType'
        rst3.message == 'duplicate.collectionId-ruleId'
        !rst4.isSuccess()
    }

}
```

