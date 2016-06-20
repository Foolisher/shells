

# Guice


## What


## Why


## How

### 注入技术

#### 普通注入

#### 注解式注入

#### 按名称注入

#### 实例化注入
  实例化注入与其他注入方式的不同在于实际被绑定对象不是有容器创建的，而是在定义注入逻辑时由用户自己实例化被注入对象，例如：
```
bind(String.class)
  .annotatedWith(Names.named("JDBC URL"))
  .toInstance("jdbc:mysql://localhost/pizza");
// 
bind(User.class)
  .annotatedWith(Names.named("MockedUser"))
  .toInstanceWIth(new User(){
    {
      this.name = "I am ET";
    }
  });
```

