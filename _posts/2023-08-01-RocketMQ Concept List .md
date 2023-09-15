---
title: RocketMQ Concept List
tags:
  - RocketMQ
  - MQ
categories:
- Midware
---

RocketMQ

# 1.Core Concept

- NameServer
- Broker
- Topic
- Consumer Group
- Consumer
- 队列负载算法
- 队列重平衡机制
- 并发消费
- 顺序消费
- 消费进度存储
- 定时消息
- 事务消息
- 消息过滤

# 2.常见问题和解决方案

1. 重复消费问题怎么办？    ==> 幂等

2. 消息会不会丢失，什么情况会导致消息丢失？

3. 高可用最佳实践是什么？

4. 高可用如何选主 （raft）

   - 第一轮选举先投票给自己，所以第一轮选举失败。
   - 随后进入一个随机事件的休眠，先苏醒的Broker投票给自己，其他Broker收到了他的选票也会投票给他。
   - 得到超过半数的选票，就可以快速选举出来一个Leader。

5. 存储数据的安全性？

6. broker、topic、tag、 MessageQueue 有什么联系，怎么协同的？

7. RocketMQ高效的几个原因，RocketMQ集群的性能瓶颈在哪里？

   pagechache、多队列、多broker

8. 容量设计要注意什么？

9. 如何拆分Topic

   - 不同类型的消息拆分，如普通消息、事务消息、定时（延时）消息、顺序消息 
   - 没有业务关联的拆分
   - 优先级不一致的拆分
   - 量级不同的拆分，类似大小表

10. 增加一组broker如何维护topic；

11. 如何备份数据？

12. commitlog的作用  ==>  redo + datafile   持久性

13. ConsumeQueue   ==> 记录消费队列的offset

14. 零拷贝

15. topic只有4个队列，出现大量的积压，怎么办？

16.  RocketMQ 消息限流怎么玩 ==>  **令牌桶算法** 

17. 一致性？？

18. 最大的特色是什么？？

19. 

# 3.规范

命名规范

容量规范

Topic申请流程

消费组申请