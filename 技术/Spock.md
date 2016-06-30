



# Spock

## 单元测试


## 什么是Spock


## JUnit


## Spock VS JUnit+Mock

## 添加依赖
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


















