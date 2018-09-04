---
layout: post
title: 网络与网际互联-《分布式系统概念与设计》
date: 2018-01-13
author: "Xsp"
catalog: true
tags:
    - 分布式
---

### 简介

+ 通信子系统：为分布式系统提供通信设施的软硬件组件
+ 主机：计算机或其他用网络进行通信的设备
+ 结点：网络上的所有计算机或交换设备

影响网络性能的参数
+ 延迟
+ 数据传输率，单位一般是 bit/s，注意和byte的区别。

### 网络类型

+ 个域网（Personal Area Network, APN）
+ 局域网（Local Area Network, LAN）
+ 广域网（Wide Area Network, WAN）
+ 城域网（Metropolitan Area Network, MAN）
+ 无线局域网（Wireless Area Network, WAN）

...

### 网络原理

#### 数据包
长度有限的二进制序列。长度有限的原因：
+ 为可能到达的最大数据包分配足够的缓冲空间
+ 长消息传递为等待通信道路空闲会出现过度延迟

> 元素迟于播放时间到达目的地，就不在有用，将被接收进程丢弃。（P51，不太懂）？

#### 交换模式
+ 广播
+ 电路交换
+ 包交换，
+ 帧中继，通过很快地交换小的数据包(帧)来解决延迟的问题。

#### 协议
为了完成给定任务，进程间通信所要用到的一组周知的规则和格式。包括两个重要的部分
+ 必须交换的信息的顺序的归约
+ 消息中数据格式的归约

一个协议是由分别位于发送方和接收方的一对软件模块所实现的。

 OSI 7层参考模型：
 + 应用层
 + 表示层
 + 会话层
 + 传输层
 + 网络层
 + 数据链路层
 + 物理层

端口：
+ 公共端口：小于1023，用于特权进程。
+ 已注册端口：1024~49151。IANA（Internet Assigned Numbers Authority，互联网号码分配局）拥有的服务描述。
+ 个人目的：~65535。实际上大于1023的端口都能用于个人目的，只是不能同时访问相应的已注册的服务。

数据包传递：
+ 数据报包传递。一次性过程，不需要计划，一旦传递，不再保存相关信息。
+ 虚电路包传递。先建立虚电路

#### 路由
除了局域网以外，例如以太网，其他网络都需要的功能。局域网在所有相连的主机间两两都有直接连接。

大型网络一般采用自适应路由，即网络两点间通信的最佳路由会周期性地重新评估。这是由网络层程序的路由算法决定。
路由算法包括两个部分：
+ 决定路径
+ 监控流量和检测配置变化或故障来动态更新网络。

RIP（router information protocol）路由算法：
```
Send: Each t seconds or when Tl changes, send Tl on each non-faulty outgoing link.
Receive: Whenever a routing table Tr is received on link n:
for all rows Rr in Tr {
    if (Rr.link   n) {
        Rr.cost = Rr.cost + 1;
        Rr.link = n;
        if (Rr.destination is not in Tl) add Rr to Tl;  // add new destination to Tl
        else for all rows Rl in Tl {
            if (Rr.destination = Rl.destination and
                (Rr.cost < Rl.cost or Rl.link = n)) Rl = Rr;
                // Rr.cost < Rl.cost : remote node has better route
                // Rl.link = n : remote node is more authoritative
        }
    }
}
```

#### 拥塞控制

避免数据包在网络中传递时经过拥塞结点而被丢弃，最好将数据包保存在发生拥塞前的结点中知道拥塞减少。

#### 网际互联
注意一下几个概念的区别：


+ 路由器，转发数据包
+ 网桥
+ 集线器，
+ 交换机

局域网内部不需要路由器？
以太网是用于局域网的技术。

#### 互联网协议

TCP（Transmission Control Protocol，传输控制协议）
UDP（User Datagram Protocol，用户数据报协议）

ARP（Address Resolution Protocol，地址解析协议），将IP地址转换为物理地址，如果底层网络是以太网的话，就是转换为以太网地址。

链路-状态算法，开放最短路径优先（Open Shortest Path First，OSPF），基于Dijkstra 路径寻找算法，比RIP收敛速度更快。

每个拓扑区域中使用一个路由协议，区域中的路由器在维护路由表时相互合作。

解决路由表爆炸：
+ 对IP地址按照地理区域进行分配
+ 默认路由

网关和路由器的区别？

无类别域间路由

未注册的地址和网络地址转换（NAT，Network Address Translation）
+ 局域网中的计算机通过具有NAT功能的路由器访问互联网
+ 动态主机配置协议（DHCP, Dynamic Host Configuration Protocol）
