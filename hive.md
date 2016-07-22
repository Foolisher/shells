# 连接方式

```
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
select * from A a left join B b on a.v=b.v
>>>
1    1
2    null
4    4
4    4

```

## right outer\[semi\] join

## full outer join

