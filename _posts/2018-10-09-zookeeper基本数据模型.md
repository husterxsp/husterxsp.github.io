---
layout: post
title: zookeeper基本数据模型
date: 2018-10-09
author: "Xsp"
catalog: true
tags:
    - Zookeeper
    - Java
---



### 基本数据模型

树形结构，类似于 linux/unix的文件目录。

每个节点称之为znode，可以有子节点，也可以有数据。

每个节点分为临时节点和永久节点，临时节点在客户端断开后消失。

每个zk节点有各自的版本号

节点数据变化时，节点的版本号累加（乐观锁）？？

删除修改过时节点，版本号不匹配则会报错

每个节点的数据不宜过大，几k即可。节点可以设置权限acl

### 作用体现

- master节点选举：master 节点挂掉之后，从节点接手工作

- 统一配置文件管理：即只部署一台服务器，把相同的配置文件同步更新到其他所有服务器（如修改redis统一配置）

- 发布与订阅：类型消息队列，dubbo把数据存在znode上，订阅者会读取这个数据

- 分布式锁：分布式环境中不同进程之间争夺资源，类似于多线程中的锁

- 集群管理：保证数据的强一致性（？？？最终一致性吧？），数据同步


### 基本命令

```
$ zkCli
Connecting to localhost:2181
Welcome to ZooKeeper!
JLine support is enabled
[zk: localhost:2181(CONNECTING) 0]
WATCHER::

WatchedEvent state:SyncConnected type:None path:null

[zk: localhost:2181(CONNECTED) 0] ls /
[zookeeper]
# ls2 显示的信息更详细
[zk: localhost:2181(CONNECTED) 1] ls2 /
[zookeeper]
cZxid = 0x0
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x0
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x0
cversion = -1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 1
# ls2 等价于 ls + stat
[zk: localhost:2181(CONNECTED) 2] stat /
cZxid = 0x0
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x0
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x0
cversion = -1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 1
# get 取数据，当前数据为空
[zk: localhost:2181(CONNECTED) 3] get /

# 节点id
cZxid = 0x0
# 创建时间
ctime = Thu Jan 01 08:00:00 CST 1970
# 修改后的id
mZxid = 0x0
# 修改后的时间
mtime = Thu Jan 01 08:00:00 CST 1970
# 子节点id
pZxid = 0x0
# 子节点version
cversion = -1
# 数据版本号，修改后累加1
dataVersion = 0
# acl 权限
aclVersion = 0
ephemeralOwner = 0x0
# 数据长度
dataLength = 0
# 子节点个数
numChildren = 1

```



### 其他命令

#### create

```
# 创建节点，默认的是持久化的，非顺序的
[zk: localhost:2181(CONNECTED) 5] create /imooc imooc-data
Created /imooc
[zk: localhost:2181(CONNECTED) 6] get /imooc
imooc-data
cZxid = 0xf
ctime = Tue Oct 09 14:55:16 CST 2018
mZxid = 0xf
mtime = Tue Oct 09 14:55:16 CST 2018
pZxid = 0xf
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 10
numChildren = 0
# 创建临时节点 -e 参数
[zk: localhost:2181(CONNECTED) 7] create -e /imooc/tmp imooc-data
Created /imooc/tmp
[zk: localhost:2181(CONNECTED) 8] get /imooc
imooc-data
cZxid = 0xf
ctime = Tue Oct 09 14:55:16 CST 2018
mZxid = 0xf
mtime = Tue Oct 09 14:55:16 CST 2018
pZxid = 0x10
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 10
numChildren = 1
# 可以看到临时节点的 ephemeralOwner 参数不一样
[zk: localhost:2181(CONNECTED) 10] get /imooc/tmp
imooc-data
cZxid = 0x10
ctime = Tue Oct 09 14:56:55 CST 2018
mZxid = 0x10
mtime = Tue Oct 09 14:56:55 CST 2018
pZxid = 0x10
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x10002484f460001
dataLength = 10
numChildren = 0
# 对于临时节点，断开客户端后，一段时间，就会删除
```



```
# 创建顺序节点 ，-s 即 sequence
[zk: localhost:2181(CONNECTED) 9] create -s /imooc/sec seq
Created /imooc/sec0000000002
[zk: localhost:2181(CONNECTED) 10] create -s /imooc/sec seq
Created /imooc/sec0000000003
[zk: localhost:2181(CONNECTED) 11] create -s /imooc/sec seq
Created /imooc/sec0000000004
[zk: localhost:2181(CONNECTED) 12] ls /imooc
[sec0000000003, sec0000000004, tmp, sec0000000002]
```



#### set

```
[zk: localhost:2181(CONNECTED) 13] get /imooc
imooc-data
cZxid = 0xf
ctime = Tue Oct 09 14:55:16 CST 2018
mZxid = 0xf
mtime = Tue Oct 09 14:55:16 CST 2018
pZxid = 0x16
cversion = 6
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 10
numChildren = 4
[zk: localhost:2181(CONNECTED) 14] set /imooc new-data
cZxid = 0xf
ctime = Tue Oct 09 14:55:16 CST 2018
mZxid = 0x17
mtime = Tue Oct 09 15:08:16 CST 2018
pZxid = 0x16
cversion = 6
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 8
numChildren = 4
[zk: localhost:2181(CONNECTED) 15] get /imooc
new-data
cZxid = 0xf
ctime = Tue Oct 09 14:55:16 CST 2018
mZxid = 0x17
mtime = Tue Oct 09 15:08:16 CST 2018
pZxid = 0x16
cversion = 6
# 修改后，版本+1
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 8
numChildren = 4
```

#### 乐观锁

```
# 修改版本1
[zk: localhost:2181(CONNECTED) 16] set /imooc 123 1
cZxid = 0xf
ctime = Tue Oct 09 14:55:16 CST 2018
mZxid = 0x18
mtime = Tue Oct 09 15:10:07 CST 2018
pZxid = 0x16
cversion = 6
dataVersion = 2
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 4
# 另一个人也修改版本1，但是前面已经改过了，所以这里就报错了。
# 乐观锁，即一开始假定不会冲突，等操作完，要提交的时候再验证，有冲突再放弃事务。
[zk: localhost:2181(CONNECTED) 17] set /imooc 123 1
version No is not valid : /imooc
```

#### delete

```
[zk: localhost:2181(CONNECTED) 9] get /imooc/sec0000000003
seq
cZxid = 0x15
ctime = Tue Oct 09 15:03:56 CST 2018
mZxid = 0x15
mtime = Tue Oct 09 15:03:56 CST 2018
pZxid = 0x15
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
# 不设置版本，直接删除节点
[zk: localhost:2181(CONNECTED) 10] delete /imooc/sec0000000003
[zk: localhost:2181(CONNECTED) 11] get /imooc/sec0000000003
Node does not exist: /imooc/sec0000000003
# delete时添加版本号，比较符合规范
# 先set一下，改一下version
[zk: localhost:2181(CONNECTED) 15] set /imooc/sec0000000002  2
cZxid = 0x14
ctime = Tue Oct 09 15:03:32 CST 2018
mZxid = 0x1d
mtime = Tue Oct 09 15:17:04 CST 2018
pZxid = 0x14
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 1
numChildren = 0
[zk: localhost:2181(CONNECTED) 16] get /imooc/sec0000000002
2
cZxid = 0x14
ctime = Tue Oct 09 15:03:32 CST 2018
mZxid = 0x1d
mtime = Tue Oct 09 15:17:04 CST 2018
pZxid = 0x14
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 1
numChildren = 0
# 然后加上版本号删除
[zk: localhost:2181(CONNECTED) 18] delete /imooc/sec0000000002 0
version No is not valid : /imooc/sec0000000002
[zk: localhost:2181(CONNECTED) 19] delete /imooc/sec0000000002 1
```



### session 基本原理

session超时即过期，临时znode会被删除、

心跳机制：client向server的ping包请求











