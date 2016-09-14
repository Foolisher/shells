# 开发实践篇

## 1. 参数检查

```
Bad Case:
if(p1==null || p2==null || p3==null){
  throw new RuntimeException("params null error");

Good Case:
checkArgument(p1!=null, "p1 is null");
checkArgument(p2!=null, "p2 is null");
...
新人发现许多老人也这样搞，那我也这样搞吧
这样的接口是不易使用的，且让用的人出现错误时很懵比，好的规则是参数分开校验
```

## 2.-

