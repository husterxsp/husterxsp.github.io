---
layout: post
title: zookeeper集群搭建
date: 2018-10-12
author: "Xsp"
catalog: true
tags:
    - Zookeeper
    - Java
---



### 伪分布式

IP还是一样，设置不同的端口，叫伪分布式是因为一个节点挂掉之后，都挂了。

### 开始搭建

还是用虚拟机搞吧。

- 下载zookeeper拷贝到 /usr/local/zookeeper  目录
- 拷贝/usr/local/zookeeper/conf/zoo_sample.cfg内容到新建的 /usr/local/zookeeper/conf/zoo.cfg

修改zoo.cfg

```bash
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
# 修改数据和log目录
dataDir=/usr/local/zookeeper/dataDir
dataLogDir=/usr/local/zookeeper/dataLogDir
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1

# 设置白名单
4lw.commands.whitelist=*
# 设置server
# 虚拟机IP：数据同步端口：选举端口
server.1=192.168.44.142:2888:3888
server.2=192.168.44.142:2889:3889
server.3=192.168.44.142:2890:3890
```

创建myid文件

```bash
$ xushaopeng@ubuntu:/usr/local/zookeeper/dataDir$ pwd
/usr/local/zookeeper/dataDir
$ xushaopeng@ubuntu:/usr/local/zookeeper/dataDir$ touch myid
# myid 文件就写入个 1
```

启动的时候可能有权限问题，所以干脆添加一下权限

```bash
$ sudo chmod -R 777 zookeeper
# 添加权限后可以启动
$ bin/zkServer.sh start
```



一个节点配置成功，然后复制 zookeeper02、zookeeper03目录

```
xushaopeng@ubuntu:/usr/local$ sudo cp zookeeper zookeeper02 -rf
xushaopeng@ubuntu:/usr/local$ sudo cp zookeeper zookeeper03 -rf
xushaopeng@ubuntu:/usr/local$ sudo chmod -R 777 zookeeper02
xushaopeng@ubuntu:/usr/local$ sudo chmod -R 777 zookeeper03
```

####  zookeeper02：

```
# /usr/local/zookeeper02/conf/zoo.cfg
# zoo.cfg 文件修改主要是文件目录和端口
dataDir=/usr/local/zookeeper02/dataDir
dataLogDir=/usr/local/zookeeper02/dataLogDir
# the port at which the clients will connect
clientPort=2182
```

myid 文件写入2



#### zookeeper03：

```
# /usr/local/zookeeper03/conf/zoo.cfg
# zoo.cfg 文件修改主要是文件目录和端口
dataDir=/usr/local/zookeeper03/dataDir
dataLogDir=/usr/local/zookeeper03/dataLogDir
# the port at which the clients will connect
clientPort=2183
```

myid写入3



配置完成。



#### 开始启动

```bash
# 分别开启3个zookeeper server
xushaopeng@ubuntu:/usr/local$ sudo zookeeper/bin/zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
xushaopeng@ubuntu:/usr/local$ sudo zookeeper02/bin/zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper02/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
xushaopeng@ubuntu:/usr/local$ sudo zookeeper03/bin/zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper03/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
# 连接其中一个
xushaopeng@ubuntu:/usr/local$ sudo zookeeper/bin/zkCli.sh -server localhost:2181
# 写入数据
[zk: localhost:2181(CONNECTED) 1] create /data 123
Created /data
[zk: localhost:2181(CONNECTED) 2] ls /
[zookeeper, data]
# 退出再连另外一个server 2182
xushaopeng@ubuntu:/usr/local$ sudo zookeeper/bin/zkCli.sh -server localhost:2182
# 可以看到刚才创建的data节点已经同步了过来
[zk: localhost:2182(CONNECTED) 0] ls /
[zookeeper, data]
[zk: localhost:2182(CONNECTED) 1] get /data
123
cZxid = 0x100000002
ctime = Fri Oct 12 04:14:03 PDT 2018
mZxid = 0x100000002
mtime = Fri Oct 12 04:14:03 PDT 2018
pZxid = 0x100000002
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
```



综上，伪分布式搭建成功



### 真实环境搭建

用三台虚拟机

直接用第一台创建克隆

三台IP分别为 

192.168.44.142

192.168.44.143

192.168.44.144

每台虚拟机修改

```bash
# /usr/local/zookeeper/conf/zoo.cfg
server.1=192.168.44.142:2888:3888
server.2=192.168.44.143:2888:3888
server.3=192.168.44.144:2888:3888
```

`/usr/local/zookeeper/dataDir/myid` 文件，对应修改为1、2、3



然后分别启动。

启动后，执行 `./zkServer.sh status` 查看启动状态

Error contacting service. It is probably not running.

注意，这个时候可能是因为才刚启动，还在投票选举的过程中，所以会报错，等会重新试一下就好了。（另外也不知道是不是dataDir和dataLogDir目录的原因，删除重新mkdir了）



```bash
xushaopeng@ubuntu:/usr/local/zookeeper$ ./bin/zkServer.sh  status
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Error contacting service. It is probably not running.
xushaopeng@ubuntu:/usr/local/zookeeper$ ./bin/zkServer.sh  status
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Mode: follower
```

查看状态，有1个leader和2个follower

接下来连接其中一个节点：

```bash
$ zkCli -server 192.168.44.142:2181
# 创建一个节点
[zk: 192.168.44.142:2181(CONNECTED) 1] create /real-cluster 123
Created /real-cluster
[zk: 192.168.44.142:2181(CONNECTED) 2] ls /
[real-cluster, zookeeper]
[zk: 192.168.44.142:2181(CONNECTED) 3] get /real-cluster
123
cZxid = 0x200000002
ctime = Sat Oct 13 16:21:11 CST 2018
mZxid = 0x200000002
mtime = Sat Oct 13 16:21:11 CST 2018
pZxid = 0x200000002
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
# 连接第2台，可以看到数据已经同步
$ zkCli -server 192.168.44.143:2181
[zk: 192.168.44.143:2181(CONNECTED) 0] ls /
[real-cluster, zookeeper]
[zk: 192.168.44.143:2181(CONNECTED) 1] get /real-cluster
123
cZxid = 0x200000002
ctime = Sat Oct 13 16:21:11 CST 2018
mZxid = 0x200000002
mtime = Sat Oct 13 16:21:11 CST 2018
pZxid = 0x200000002
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
# 第3台也同步了。
```



测试选举：

上述status为leader的节点，执行 `/bin/zkServer.sh  restart` 重启，然后查看状态，可以看到leader已经变了。









