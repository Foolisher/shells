



# Spock
Spock是一个基于Java与Groovy语言的测试框架，借助于Groovy优雅的表达式语言与独具一格的测试模式使它脱颖而出，


## 单元测试


## 什么是Spock


## JUnit


## Spock VS JUnit+Mock


## 实践

### 添加依赖
```

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
```
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















