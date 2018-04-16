## 是否运行unclean leader

### 什么情况产生unclean leader

* followers and leader are all out-of-sync and crashed

### min.insync.replicas

leader用于保证最小followers可用数量，如果followers可用数量小于该值，broker停止服务，Producer无法生产数据，broker不提供消息写服务

### acks 配置

* acks=0 发送到网络即返回
* acks=1 leader有应答即返回
* acks=all 所有replicas有应答才返回

## 生产者重试





