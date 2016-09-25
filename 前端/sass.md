## 模块化

## 嵌套实现粒度控制

```
.widget{
    .header{
        ...
    }
}
```

## @include 实现复杂模块解耦

```
@include radius(5px);
```

## @mixins 实现函数定义

```
@mixins raduis($pixels){
 -o-bordre-radius: $pixels;
 -moz-border-radius: $pixels;
 ...
}
```

## 全局遍历实现全局主题控制

```
$fontSize: 14px
$fontFamily: 'Yahei'

```

