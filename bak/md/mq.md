# 消息队列

问题场景

1. 系统崩溃
2. 服务处理能力有限
3. 链路耗时长尾
4. 日志如何处理

> 消息队列：一个保存消息的容器，本质是个队列，需要支持高吞吐，高并发，并高可用

## 发展

标准

- AMQP

实现

- Kafka
- RocketMQ
- Pulsar

## Kafka

**使用场景**

![image-20220525012627248](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220525012627248.png)

**如何使用**

1. 创建集群
2. 新增Topic
3. 编写生产者逻辑
4. 编写消费者逻辑

**基本概念**

![image-20220525012900056](https://raw.githubusercontent.com/Usigned/pic-typora/main/images/image-20220525012900056.png)

- topic：逻辑队列
- Cluster：物理集群，每个集群可以建立多个不同集群
- Producer: 生产者，将业务消息发送到Topic
- Consumer: 消费者，负责消费Topic中的消息
- ConsumerGroup：消费者组，不同组的**消费进度**互不干涉

- partion: topic的分片，分片可以并行处理，提高topic的吞吐

> zookeeper：负责存储集群的元信息，包括分区分配信息

**优化**

> producer -> broker -> consumer

- Producer
  - 批量发送
  - 消息数据压缩

- Broker
  - 顺序写
  - 消息索引
  - 零拷贝
- Consumer
  - rebalance

> 什么是零拷贝？
>
> HDFS

