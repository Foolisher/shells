

# Stream
在Java8中的 `Stream` 提供了针对集合非常便利的操作模式，不但提供了数据处理管道模式，还提供了数据聚合功能，同时结合lambda新特性，许多对集合处理需要大篇幅代码的场景，或许Stream一行就能搞定，对于代码我们一直最求的就是开发效率和可读性，而Stream它做到了！

同时Stream还支持普通流处理与并发流处理，并发流处理的效率基本接近CPU核数倍数的效率提神，对于一些计算场景，若我们使用并发流，将会更大程度利用多核CPU优势提高处理效率

## 使用场景

### 流的操作

+ **Intermediate**:
```
map (mapToInt, flatMap 等)、 filter、 distinct、 sorted、 peek、 limit、 skip、 parallel、 sequential、 unordered
```

+ **Terminal**:
```
forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、 anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator
```

+ **Short-circuiting**:
```
anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 limit
```
## 责任链模式


## 并发流



