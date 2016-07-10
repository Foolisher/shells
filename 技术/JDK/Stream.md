


# Stream
在jdk中有许多集合类数据结构，也提供了丰富的集合操作api，但是他们对一些复杂的转换处理上依然显得笨重与繁琐，且api对数据处理上并没有提供一些直观便捷的操作，于是后来commons、guava诞生了，他们为方便迭代并处理集合数据而生，但是若期望对数据进行多次处理，或一些归并计算时依然是无法畅快的使用，例如我们大多情况期望对集合进行过滤、去重、转换、合并等一些列有序的操作，即使用到Guava也许经过许多次迭代转换才能实现，而用到java8的Stream或许一行就可搞定！

在Java8中的 `Stream` 提供了针对集合非常便利的操作模式，不但提供了数据处理管道模式，还提供了数据聚合功能，同时结合lambda新特性，许多对集合处理需要大篇幅代码的场景，或许Stream一行就能搞定，对于代码我们一直最求的就是开发效率和可读性，而Stream它做到了！

同时Stream还支持普通流处理与并发流处理，并发流处理的效率基本接近CPU核数倍数的效率提神，对于一些计算场景，若我们使用并发流，将会更大程度利用多核CPU优势提高处理效率，Java8的并发流依然是基于fork/join的，这样的并发模型即适应任务的切分，也能对结果进行回溯合并。

Map/Reduce模型我们已经知道这是在Hadoop中应用的一个并行大数据计算模型，Map是Hadoop中一个最小粒度的任务数据处理过程，map是数据管道过程，Reduce是将在相同组的map后结果进行合并，reduce是聚合过程

## 使用场景

### 流的操作
Stream分为动作类api和聚合类api，动作类api是对数据上定义的一个个处理动作，他们会按设定的顺序依次执行，这类动作具有惰性，他们在定义时并没有发生什么，只是定义了一系列的处理逻辑

还有一类是终结api，我想把它称作收集api，它们的目的是设定流式计算后的一种收集方式，例如对于求和那么是讲所有值加起来，或取最大值最小值，或分组等，每一次流式计算有且仅有一个收集api

+ **Intermediate**:
```java
map (mapToInt, flatMap 等)、 filter、 distinct、 sorted、 peek、 limit、 skip、 parallel、 sequential、 unordered
```

+ **Terminal**:
```java
forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、 anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator
```

+ **Short-circuiting**:
```java
anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 limit
```

### 案例1
```java




		// 需求: 对一个地址表中求每个地区的平均年龄
		// SELECT SUM(age),address
		//   FROM user_infos
		// GROUP BY address

		Object rst = Stream.of(
						"lee,23,hz",
						"alen,28,newyork",
						"hanm,25,beijing",
						"lei,24,beijing")
						.map(l -> l.split(","))
						.collect(Collectors.groupingBy(line->line[2], Collectors.summingInt(o->Integer.parseInt(o[1]))))
						;

```


## 责任链模式


## 并发流



