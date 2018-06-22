---
layout: post
title: java消息中间件
date: 2018-06-20
author: "Xsp"
catalog: true
tags:
    - Java
---


[https://www.imooc.com/video/15215](https://www.imooc.com/video/15215)

### 消息中间件的好处
- 解耦
- 异步
- 缓冲
- 横向扩展：可以同时启动多个客户端来读取消息队列
- 安全可靠：消息中间件会保存消息队列
- 顺序保证：比如Kafka

### 概述

- 中间件： 将具体业务和底层逻辑解耦的组件。
- 消息中间件：关注数据的发送和接收。
- JMS：Java消息服务（Java Message Service）应用程序接口是一个Java平台中关于面向消息中间件（MOM）的API，用于在两个应用程序之间，或分布式系统中发送消息，进行异步通信。Java消息服务是一个与具体平台无关的API，绝大多数MOM提供商都对JMS提供支持。
- AMQP：高级消息队列协议(Advanced Message Queuing Protocol), 对于面向消息中间件的应用层协议。

#### JMS & AMQP 对比

<img src="/img/post/2018-06-20-java-message-middleware-1.png" style="width:100%;max-width: 700px;text-align:center;display:block;">

#### 常见消息中间件对比

##### ActiveMQ

Apache ActiveMQ是Apache软件基金会所研发的开放源代码消息中间件。

##### RabbitMQ

RabbitMQ是实现了高级消息队列协议（AMQP）的开源消息代理软件（亦称面向消息的中间件）。RabbitMQ服务器是用Erlang语言编写的，

##### Kafka

发布订阅消息系统、分布式日志服务。本身是做日志储存的，所以对消息的顺序有严格的要求。

开源流处理平台，由Scala和java编写，目标是为处理实时数据提供一个统一、高吞吐、低延迟的平台。其持久化层本质上是一个“按照分布式事务日志架构的大规模发布/订阅消息队列”。

特性：
- O(1)时间消息的持久化
- 高吞吐量，即使普通的硬件也支持每秒百万的消息
- 分区、消费者分组

##### 综合对比：
<img src="/img/post/2018-06-20-java-message-middleware-2.png" style="width:100%;max-width: 700px;text-align:center;display:block;">

### JMS规范

- 提供者：实现JMS规范的消息中间件
- 客户端：发送或接收消息的应用程序
- 生产者(订阅者)：创建并发送消息的客户端
- 消费者(订阅者)：接收并处理消息
- 消息：传递的数据内容
- 消息模式：传递消息的方式。JMS定义了主题和队列两种模式。

#### 消息模式

队列模式
- 生产者和消费者
- 队列中的消息只有一个消费者消费
- 消费者可以随时消费

主题模式
- 发布者和订阅者
- 消息被所有订阅者消费
- 订阅者不能消费订阅之前就发送到主题中的消息。

#### 编码接口

- ConnectionFactory 创建连接到消息中间件的连接工厂
- Connection 通信链路
- Destination 消息发布和接收的地点，包括队列和主题
- Session 会话，表示一个单线程的上下文
- MessageConsumer 会话创建，用于接收消息
- MessageProducer 会话创建，用于发送消息
- Message 消息对象，包括消息头，消息属性，消息体

<img src="/img/post/2018-06-20-java-message-middleware-3.png" style="width:100%;max-width: 700px;text-align:center;display:block;">

一个Connection 可以创建多个会话，即一个连接可以供多个线程使用。


### 使用activemq

#### mac 安装

http://activemq.apache.org/getting-started.html#GettingStarted-UsingHomebrewinstalleronOSX

```
$ brew install apache-activemq
```

安装到了 `/usr/local/Cellar/activemq/` 目录

启动
```
$ activemq start
```

之后可以访问 http://localhost:8161/

#### 队列模式演示

消息被均衡分发给多个消费者

#### 主题模式演示

启动时需注意先启动消费者，即先订阅，之后所有的消费者会接收相同的消息

#### Spring JMS 理论

使用Spring 继承JMS连接ActiveMQ
- ConnectionFactory 管理连接的连接工厂
    - Spring提供的连接池
    - JmsTemplate每次发消息都会重新创建连接，会话和productor
    - SingleConnectionFactory 整个应用共用一个connection
    - CacheConnectionFactory
- JmsTemplate 用于发送和接收消息的模板类
    - spring提供，直接注入就可使用
    - 线程安全，可以在整个应用使用
- MessageListener 消息监听器
    - 实现onMessage方法

#### 实践代码



### 大型系统中的实践

#### ActiveMQ 集群

为什么要集群？
- 高可用，排除单点故障引起的系统中断
- 负载均衡

集群方式
- 客户端集群，多个消费者消费同一个队列
- Broker cluster, 多个Broker 之间同步消息
- Master slave， 高可用

ActiveMQ失效转移
- 在消息服务器宕机时，客户端在传输层重新连接到其他服务器。


Broker Cluster 集群方式


### 其他消息中间件


### 参考

- [为什么分布式一定要有消息队列？- mp.weixin.qq.com](https://mp.weixin.qq.com/s/-7SD1BfxUct7TgIjxPvDSg)
- [中间件是什么？- 知乎](https://www.zhihu.com/question/19730582)
- [Java消息服务 - wikipedia](https://zh.wikipedia.org/wiki/Java%E6%B6%88%E6%81%AF%E6%9C%8D%E5%8A%A1)
- [AMQP - wikipedia](https://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol)
- [RabbitMQ - wikipedia](https://zh.wikipedia.org/wiki/RabbitMQ)
- [ActiveMQ - wikipedia](https://zh.wikipedia.org/wiki/Apache_ActiveMQ)
- [Kafka - wikipedia](https://zh.wikipedia.org/wiki/Kafka)
