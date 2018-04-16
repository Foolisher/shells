## 1. Broker如何保证消息可靠性

### 是否运行unclean leader

### 什么情况产生unclean leader

* followers and leader are all out-of-sync and crashed

### min.insync.replicas

leader用于保证最小followers可用数量，如果followers可用数量小于该值，broker停止服务，Producer无法生产数据，broker不提供消息写服务

### acks 配置

* acks=0 发送到网络即返回
* acks=1 leader有应答即返回
* acks=all 所有replicas有应答才返回

## 2. 生产者如何保证消息可靠性

### 生产者重试

哪些情况下可以自己决定重试

1. broker未应答，网络问题，可能造成消息重复，如果Broker自己能解决去重的话，就没问题，消费者自己应做好幂等

哪些情况下应用无需再重试

1. 权限问题
2. 消息体错误，消息体太大，格式错误等
3. 非broker返回的错误，自己处理

## 3. 消费者如何保证消息可靠性

1. commit-by-period，consumer消费期间crash，period期间内的消息会重复消费 
   `auto.commit.interval.ms`

2. commit-offset after consuming，如果commit-offset失败，消息重复消费
3. commit-offset before consuming，如果consuming失败，消息漏消费，问题严重



