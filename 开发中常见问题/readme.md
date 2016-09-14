## 1 参数检查

```
Bad Case:
if(p1==null || p2==null || p3==null){
  throw new RuntimeException("params null error");

Good Case:
checkArgument(p1!=null, "p1 is null");
checkArgument(p2!=null, "p2 is null");
...
新人发现许多老人也这样搞，那我也这样搞吧
```

2.

