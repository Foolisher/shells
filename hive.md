# 连接方式

```
表数据示例

Table A 
1 
2 
4 

Table B 
1 
3 
4 
4
```

## join

```
-- 普通join会判断连边条件都符合的记录

select * from A a
join B b on a.v = b.v

>>>
a.v    b.v
1      1
4      4
4      4

```

## left outer\[semi\] join

```
-- 始终以左边表为参照，去找右边匹配的记录，即使找不到，左边表记录依然存在
-- 只是右表的字段会为Null

select * from A a left join B b on a.v=b.v
>>>
1    1
2    null
4    4
4    4

```

## right outer\[semi\] join

```
-- 与 left join 类似，只是它以右表为参照表，对右表记录保全

1    1
null 3
4    4
4    4   
```

## full outer join

```
-- 从效果上来说，他是左连接+右连接后union all的结果

1    1
2    null
null 3
4    4
4    4

```

## union

```

-- union 会对两表的结果进行排序，去重，若在需要去重的场景可以用union
-- 数据库引擎是希望把它们当做一个表的结果来处理

select * from A
union 
select * from B

>>>
1
2
3
4
```

## union all

```
-- union all只是简单的结果拼接，不把他们当做一个表来处理

select * from A
union all
select * from B
1
2
4
1
3
4
4
```

