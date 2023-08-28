---
title: Kafka&RocketMQ
tags:
  - Kafka
  - RocketMQ
  - MQ
categories:
- Midware
---

没有最好，只有最适合！



| 对比项                 |                            Kafka                             | RocketMQ                           | 描述 |
| ---------------------- | :----------------------------------------------------------: | ---------------------------------- | ---- |
| Consumer自动提交位移   |    enable.auto.commit = true auto.commit.interval.ms=5000    | persistConsumerOffsetInterval=5000 |      |
| Consumer手动动提交位移 |       enable.auto.commit = false  consumer.commitSync        |                                    |      |
| Consumer位移主题压缩   | 这个后台线程叫 Log Cleaner自动做Compaction，压缩位移消息，如果线程挂了就会导致消息无法删除不停的积压 |                                    |      |
| Coordinator            |                           Broker端                           | NameServer定时任务                 |      |
| 重复消费               |                    重平衡   Broker Crash                     | 重平衡   Broker Crash              |      |
|                        |                                                              |                                    |      |
|                        |                                                              |                                    |      |
|                        |                                                              |                                    |      |
|                        |                                                              |                                    |      |





生产者发送选择分区、队列机制

**Kafka：**

不给key就是轮询，给key就是hash！

轮询策略 

按消息键保序策略

自定义

**RockeMQ：**

轮询

自定义





消费者

RocketMQ

**系统自带负载算法**

AllocateMessageQueueAveragely

AllocateMessageQueueAveragelyByCircle

AllocateMachineRoomNearby

AllocateMessageQueueByConfig

AllocateMessageQueueByMachineRoom

**AllocateMessageQueueConsistentHash**









消费位移提交的思考：

自动提交：

​	优点，定时分批，无需代码控制管理简单

​	缺点，系统自动控制，consumer crash容易丢失位移数据导致重复消费；

​                无消息可消费时，也会提交同样的offset造成额外的消息提交；

手工提交：

​	优点，可控，没有消息消费就不会提交offset数据；

​	缺点，对代码有入侵，需要手工调用consumer.commitSync方法，Consumer 程序会处于阻塞状态，直到远端			的 Broker 返回提交结果，如果忘记调用位移数据可能出现消费异常，重复消费等问题；

综上，自动提交其实更好，也是kafka和rocketmq的默认行为，普通开发者使用自动方式就可以了，高级开发者有特殊需要可以自己控制，一般不建议使用手工提交。


RocketMQ，可以手工提交位移吗？如何控制


Kafka， Consumer Group 和 Standalone Consumer  的区别？

 Kafka Java Consumer 就是单线程的设计 ！！！ 那RocketMQ是多线程的默认20？

 Kafka 0.10.1.0 版本开始，KafkaConsumer 就变为了双线程的设计，即**用户主线程和心跳线程**。 

