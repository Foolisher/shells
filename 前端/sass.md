## 模块化

sass结合了可编程性，模块化特性，带我们进入了css的模块化历程，是css也可动态化、模块化，是css更便于管理，开发更清晰，规避了常见错误，良好的模式避免了错误，提高效率，适用于大型项目

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
$borderStyle: 1px solid lightgray;
...
```

