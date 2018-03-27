---
title: 666秒入门Rocketmq
date: 2018-03-27 22:15:34
categories: [中间件]
tags: [消息队列, Rocketmq]
---

### Rocketmq是什么？

<!-- more -->

![](666秒入门Rocketmq/namesrv-broker-producer-consumer.png)

- 是一个队列模型的消息中间件，具有高性能、高可靠、高实时、分布式特点
- Producer、Consumer、队列都可以分布式
- Producer向一些队列轮流发送消息，队列集合称为Topic，Consumer如果做广播消费，则一个consumer实例消费这个Topic对应的所有队列，如果做集群消费，则多个Consumer实例平均消费这个topic对应的队列集合
- 能够保证严格的消息顺序
- 提供丰富的消息拉取模式
- 高效的订阅者水平扩展能力
- 实时的消息订阅机制
- 亿级消息堆积能力
- 较少的依赖

### rocketmq部署物理结构

![](666秒入门Rocketmq/pic-1.png)

**rocketmq部署结构特点：**
- Name Server是一个几乎无状态节点，可集群部署，节点之间无任何信息同步。
- Broker部署相对复杂，Broker分为Master与Slave，一个Master可以对应多个Slave，但是一个Slave只能对应一个Master。Master可以部署多个，每个Broker与Name Server集群中的所有节点建立长连接，定时注册Topic信息到所有Name Server。
- Producer与Name Server集群中的其中一个节点（随机选择）建立长连接，定期从Name Server取Topic路由信息，并向提供Topic服务的Master建立长连接，且定时向Master发送心跳。Producer完全无状态，可集群部署。
- Consumer与Name Server集群中的其中一个节点（随机选择）建立长连接，定期从Name Server取Topic路由信息，并向提供Topic服务的Master、Slave建立长连接，且定时向Master、Slave发送心跳。Consumer既可以从Master订阅消息，也可以从Slave订阅消息，订阅规则由Broker配置决定。

### Rocketmq逻辑部署结构

![](666秒入门Rocketmq/pic-2.png)

**rocketmq启动停止命令**
```
nohup sh bin/mqnamesrv &       启动name服务
sh bin/mqshutdown broker       关闭name服务

nohup sh bin/mqbroker -n localhost:9876 &  启动broker服务
sh bin/mqshutdown namesrv      关闭broker服务
```

