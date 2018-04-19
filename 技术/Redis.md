## 存储模式

### poin-in-time dump

dump 策略

### apend-only-file

flush 策略

如何解决臃肿问题

如何解决恢复慢问题

如何解决漏数据问题

### master/slave模式

容灾

读写分离，read from slaves

## Sharding

数据分区，将数据split to其他node（机器）上去，极大提供数据并发读写能力提高性能的同时，还能做到一定的分区容忍性，部分机器可用，持续提供服务

## Scaling

### master-master

应用在读写都差不多的情况，而且读写的qps都很高，才使用双主提供同时读写请求

如何解决有的master下线问题？

如果用原生ShardedJedis，这样的模式当节点变动时会有什么影响？

> 新节点接入后，将会对key rehash，常常会导致同一个key路由到不同的node去写入，但是旧的node里面里面将存这这个key的旧值，如果用指定node的方式读到这台机器，将会读到脏数据。目前可用一致性hash环解决该问题，或者clean all old data。

### master-slave



