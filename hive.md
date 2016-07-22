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
1    1
null 3
4    4
4    4   
```

## full outer join

```
1    1
2    null
null 3
4    4
4    4

```

## union

```
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

