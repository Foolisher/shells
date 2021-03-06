# 《Stream》

![](http://fuxiao.oss-cn-shanghai.aliyuncs.com/book/%E9%9B%86%E5%90%88%E5%B7%A5%E5%85%B7%E8%BF%9B%E5%8C%96.png)

## 1.特征

在jdk中有许多集合类数据结构，提供了丰富的集合api，但是它们对一些复杂的转换处理上依然显得笨重与繁琐，且这些容器对数据处理上并没有提供一些直观便捷的操作，于是后来commons、guava诞生了，他们为方便迭代并处理集合数据而生，但是若期望对数据迭代地进行多次加工处理，或一些归并计算时依然是无法畅快的使用，例如我们大多情况期望对集合进行过滤、去重、转换、合并等一系列有序的操作，即使用到Guava也需经过许多次迭代转换才能实现，而用到java8的Stream或许一行就可搞定！

在Java8中的 `Stream` 提供了针对集合非常便利的操作模式，不但提供了数据处理管道模式，还提供了数据聚合功能，同时结合**lambda**新特性，许多对集合处理需要大篇幅代码的场景，这样或许`Stream`一行就能搞定，对于代码我们一直最求的就是开发效率和可读性，而Stream它做到了！

同时Stream还支持普通流处理与并发流处理，并发流处理的效率基本接近CPU核数倍数的效率提升，对于一些计算场景，若我们使用并发流，将会更大程度利用多核CPU优势提高处理效率，Java8的并发流依然是基于fork\/join的，这样的并发模型即适应任务的切分，也能对结果进行回溯合并。

Map\/Reduce模型我们已经知道这是在Hadoop中应用的一个并行大数据计算模型，Map是一个最小粒度的任务数据处理过程，map是数据管道过程，Reduce是将在相同组的数据map后结果进行归并，reduce是一个聚合过程，fork\/join看起来有点像是一个本地版的map\/reduce，而且Stream也直接提供了map\/reduce，看来这种计算模型不仅适用于分布式计算也适用于本地多并行计算，许多语言甚至在一开始就支持这样的api了

### 1.1.与普通集合的区别

1. stream本身并不存储数据，数据是存储在对应的collection里，或者在需要的时候才生成的；
2. stream不会修改数据源，总是返回新的stream；
3. stream的操作是懒执行\(lazy\)的：仅当最终的结果需要的时候才会执行，比如上面的例子中，结果仅需要前3个长度大于7的字符串，那么在找到前3个长度符合要求的字符串后，`filter()`将停止执行；

## 2.使用场景

> 如果对集合进行多次迭代加工，并且还需对预处理后的记过进行归并计算，那么java8 Stream是最好的选择；

### 2.1.**创建Stream的方式**

1\) 从array或list创建stream：

```java
Stream<Integer> integerStream = Stream.of(10, 20, 30, 40);
String[] cityArr = {"Beijing", "Shanghai", "Chengdu"};
Stream<String> cityStream = Stream.of(cityArr);
Stream<String> nameStream = Arrays.asList("Daniel", "Peter", "Kevin").stream();
Stream<String> cityStream2 = Arrays.stream(cityArr, 0, 1);
Stream<String> emptyStream = Stream.empty();
```

2\) 通过`generate`和`iterate`创建无穷stream：

```java
Stream<String> echos = Stream.generate(() -> "echo");
Stream<Integer> integers = Stream.iterate(0, num -> num + 1);
// 创建无穷流必须制定limit
```

3\) 通过其它API创建stream：

```java
Stream<String> lines = Files.lines(Paths.get("test.txt"))
String content = "AXDBDGXC";
Stream<String> contentStream = Pattern.compile("[ABC]{1,3}").splitAsStream(content);
```

### 2.2.流的操作

Stream分为动作类api和聚集类api，动作类api是对数据上定义的一个个处理动作，他们会按设定的顺序依次执行，这类动作具有惰性，他们在定义时并没有发生什么，只是定义了一系列的处理逻辑而已

还有一类是终结api，我想把它称作收集api，它们的目的是设定流式计算后的一种收集方式，例如对于求和那么是讲所有值加起来，或取最大值最小值那就保留最大、小值，或分组等，每一次流式计算有且仅有一个收集api

* **Intermediate**:

  ```java
  map (mapToInt, flatMap 等)   // 用于对数据转换  e.g. (string) => int
  filter                       // 数据过滤，如: (v) => notEmpty(v)
  distinct                     // 数据去重 
  sorted                       // 排序
  peek、 limit、 skip、 
  parallel                     // 开始并发模式
  sequential、 unordered
  ```

* **Terminal**:

  ```java
  forEach/forEachOrdered       // 对结果进行迭代，一般用于结果集进行处理货仅仅访问
  toArray                      // 将结果集转换为数组
  reduce                       // 聚合结果集，例如求和
  collect                      // 当期望对数据集进行按自定义方式收集起来，例如分组，求均值等
  min、 max、 count、          //  数学运算类求值
  anyMatch、 allMatch、 noneMatch  // 检验类api
  findFirst、 findAny、 iterator   // 对结果集访问
  ```

* **Short-circuiting**:

  ```java
  anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 limit
  ```


### 案例1

```java

 // 需求: 对一个地址表中求每个地区的平均年龄
 // SELECT AVG(age),address
 //   FROM user_infos
 //  WHERE address != ''
 // GROUP BY address

 // A. Java6/7 实现
        List<String> rawRows = new ArrayList<>();
        rawRows.add("lee,23,hz");
        rawRows.add("alen,28,newyork");
        rawRows.add("hanm,25,beijing");
        rawRows.add("lei,24,beijing");
        rawRows.add("silen,27,");

        // 1. 分隔字符串为行
        List<String[]> rows = new ArrayList<>();
        for (String rawRow : rawRows) {
            String[] cols = rawRow.split(",");
            if (cols.length == 3 && !isNullOrEmpty(cols[2]))
                rows.add(rawRow.split(","));
        }
        // line => line.split(",")

        // 2. 按地区分组
        Map<String, List<String[]>> byAddr = new HashMap<>();
        for (String[] row : rows) {
            List<String[]> rows1 = byAddr.get(row[2]);
            if (rows1 == null) {
                rows1 = new ArrayList<>();
                byAddr.put(row[2], rows1);
            }
            rows1.add(row);
        }
        // groupBy(addr): (map, line) => map.add(line::addr, line)

        // 3. 按分组聚合每组数据
        Map<String, Double> avg = new HashMap<>();
        for (String addr : byAddr.keySet()) {
            double         ageSum = 0, ageAve = 0;
            List<String[]> rows2  = byAddr.get(addr);
            for (String[] line : rows2) {
                ageSum += Integer.parseInt(line[1]);
            }
            ageAve = ageSum / rows2.size();
            avg.put(addr, ageAve);
        }
        // map,row => map.put(addr, (age1+age2, num++))
        // map: (addr,[sum,num]) => (addr, sum/num)


 // B. Java8 Stream实现版
 Object rst = Stream.of(
                "lee,23,hz",
                "alen,28,newyork",
                "hanm,25,beijing",
                "lei,24,beijing",
                "silen,27,")
                .map(l -> l.split(","))
                .filter(l -> l.length == 3 && !isNullOrEmpty(l[2]))
                .collect(Collectors.groupingBy(line -> line[2], Collectors.averagingInt(o -> Integer.parseInt(o[1]))));

>> {hz=23.0, newyork=28.0, beijing=24.5}

 // C. scala 版本
 val rst = Seq(
   "lee,23,hz",
   "alen,28,newyork",
   "hanm,25,beijing",
   "lei,24,beijing",
   "silen,27,")
   .map(line => line.split(","))
   .filter(line => line.length == 3 && line(2).nonEmpty)
   .groupBy(l => l(2)).map(row => {
   (row._1, row._2.foldLeft(0)((a, arr) => arr(1).toInt + a))
 })

```

## 责任链模式

Stream主要体现在对责任链模式的精巧设计上，对处理任务实现了灵活编排，提供统一的接口。在java web中我们其实早已领教过责任链模式了，一个个的Filter组成一个链式处理方案，自己处理完后交给下一个接力。

责任链模式是一种优秀的思想，给客户端提供一致的api，任务任意灵活编排，例如我们可以借鉴这种思想在下单逻辑的流程中，可以使用在招商报名流程中，保证了任务的顺序关系，同时将实现与控制逻辑分离，任务可增可减，实现流程编排。

![](http://fuxiao.oss-cn-shanghai.aliyuncs.com/book/%E8%B4%A3%E4%BB%BB%E9%93%BE%E6%A8%A1%E5%BC%8F.png)

### 角色

* **Client**：客户端

* **Handler**：抽象处理者

* **ConcreteHandler**：具体处理者，它含有下一个处理这的引用


更多参考:

https:\/\/nkcoder.github.io\/2016\/01\/24\/java-8-stream-api\/

https:\/\/www.ibm.com\/developerworks\/cn\/java\/j-lo-java8streamapi\/

