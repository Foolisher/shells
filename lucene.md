## 倒排索引

### 索引结构

```yaml
$field:   # 字段名称
  $term:  # 单词
    $freq: $docId^$positions  # 词频倒排序，文档id^在文档中的位置


title: 
  好吃: 
    4: 999901^1,8,99,223;79923^10,23,88,103
    2: 65356^32,45432;878^3,22
```



