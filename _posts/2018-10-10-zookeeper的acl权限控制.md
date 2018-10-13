---
layout: post
title: zookeeper的ACL权限控制
date: 2018-10-10
author: "Xsp"
catalog: true
tags:
    - Zookeeper
    - Java
---



ACL（access control lists） 

针对节点设置相关读写权限，保障数据安全性



- getAcl：获取某个节点的acl权限信息
- setAcl：设置某个节点的acl权限信息
- addauth：输入认证授权信息

acl通过3个[scheme : id : permissions] 构成

- scheme：代表某种权限机制
  - world：world下只有一个用户，即 anyone。组合写法  world:anyone:[permission]
- id：允许访问的用户
- permission：权限组合字符串

```
[zk: localhost:2181(CONNECTED) 7] getAcl /imooc/abc
'world,'anyone
: cdrwa
```



auth 和digest

- auth：代表认证登录，需要注册用户。auth:user:password:[permission]
- digest：需要对密码加密才能访问。digest:username:BASE64(SHA1(password)):[permission]
- auth是明文，digest密文。
  - setAcl /path auth:lee:lee:cdrwa 和 setAcl /path digest:lee:BASE64(SHA1(password)):cdrwa等价
  - addauth biggest lee:lee 可操作



- ip：限制IP访问。如 ip:192.168.1.1:[permission]
- super：代表超级管理员，拥有所有权限



### ACL 构成



#### world:anyone

权限字符串缩写 crdwa

- create：创建子节点
- read：获取节点、子节点
- write：设置节点数据
- delete：删除子节点
- admin：设置权限





```bash
[zk: localhost:2181(CONNECTED) 2] getAcl /imooc/abc
'world,'anyone
: cdrwa
# 设置权限
[zk: localhost:2181(CONNECTED) 3] setAcl /imooc/abc world:anyone:crwa
cZxid = 0x3b
ctime = Wed Oct 10 10:06:57 CST 2018
mZxid = 0x3b
mtime = Wed Oct 10 10:06:57 CST 2018
pZxid = 0x3b
cversion = 0
dataVersion = 0
aclVersion = 1
ephemeralOwner = 0x0
dataLength = 4
numChildren = 0
# 可以看到权限已修改
[zk: localhost:2181(CONNECTED) 4] getAcl /imooc/abc
'world,'anyone
: crwa
# 创建一个子节点
[zk: localhost:2181(CONNECTED) 5] create /imooc/abc/xyz 123
Created /imooc/abc/xyz
# 可以看到子节点无法删除
[zk: localhost:2181(CONNECTED) 6] delete /imooc/abc/xyz
Authentication is not valid : /imooc/abc/xyz
# 重新添加 删除子节点权限 d
[zk: localhost:2181(CONNECTED) 7] setAcl /imooc/abc world:anyone:rda
cZxid = 0x3b
ctime = Wed Oct 10 10:06:57 CST 2018
mZxid = 0x3b
mtime = Wed Oct 10 10:06:57 CST 2018
pZxid = 0x3f
cversion = 1
dataVersion = 0
aclVersion = 2
ephemeralOwner = 0x0
dataLength = 4
numChildren = 1
# 获取权限
[zk: localhost:2181(CONNECTED) 8] getAcl /imooc/abc
'world,'anyone
: dra
# 可以删除
[zk: localhost:2181(CONNECTED) 9] delete /imooc/abc/xyz
# 设置只有a权限，即只可以设置权限的权限
[zk: localhost:2181(CONNECTED) 10] setAcl /imooc/abc world:anyone:a
cZxid = 0x3b
ctime = Wed Oct 10 10:06:57 CST 2018
mZxid = 0x3b
mtime = Wed Oct 10 10:06:57 CST 2018
pZxid = 0x42
cversion = 2
dataVersion = 0
aclVersion = 3
ephemeralOwner = 0x0
dataLength = 4
numChildren = 0
[zk: localhost:2181(CONNECTED) 11] getAcl /imooc/abc
'world,'anyone
: a
# 不能读
[zk: localhost:2181(CONNECTED) 12] get /imooc/abc
Authentication is not valid : /imooc/abc
# 不能写
[zk: localhost:2181(CONNECTED) 13] set /imooc/abc 123
Authentication is not valid : /imooc/abc
```

#### auth 、digest

##### auth

```bash
[zk: localhost:2181(CONNECTED) 12] getAcl /names/imooc
'world,'anyone
: cdrwa
# 设置出错，需要先注册
[zk: localhost:2181(CONNECTED) 13] setAcl /names/imooc auth:imooc:imooc:cdrwa
Acl is not valid : /names/imooc
# 添加用户
[zk: localhost:2181(CONNECTED) 0] addauth digest imooc:imooc
# 再次设置
[zk: localhost:2181(CONNECTED) 1] setAcl /names/imooc auth:imooc:imooc:cdrwa
cZxid = 0x48
ctime = Fri Oct 12 09:10:11 CST 2018
mZxid = 0x48
mtime = Fri Oct 12 09:10:11 CST 2018
pZxid = 0x48
cversion = 0
dataVersion = 0
aclVersion = 1
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
# 密码为密文
[zk: localhost:2181(CONNECTED) 3] getAcl /names/imooc
'digest,'imooc:XwEDaL3J0JQGkRQzM0DpO6zMzZs=
: cdrwa
# 第一次addauth之后，后续的都是基于这个auth
[zk: localhost:2181(CONNECTED) 6] setAcl /names/imooc auth::cdrwa
cZxid = 0x48
ctime = Fri Oct 12 09:10:11 CST 2018
mZxid = 0x48
mtime = Fri Oct 12 09:10:11 CST 2018
pZxid = 0x48
cversion = 0
dataVersion = 0
aclVersion = 2
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
[zk: localhost:2181(CONNECTED) 7] getAcl /names/imooc
'digest,'imooc:XwEDaL3J0JQGkRQzM0DpO6zMzZs=
: cdrwa
```

##### digest

先退出当前用户（command+C）

```bash
[zk: localhost:2181(CONNECTED) 0] create /names/test 111
Created /names/test
[zk: localhost:2181(CONNECTED) 1] getAcl /names/test
'world,'anyone
: cdrwa
# 这里设置权限用的是上面的密文
[zk: localhost:2181(CONNECTED) 2] setAcl /names/test digest:imooc:XwEDaL3J0JQGkRQzM0DpO6zMzZs=:cdrwa
cZxid = 0x70
ctime = Fri Oct 12 15:09:02 CST 2018
mZxid = 0x70
mtime = Fri Oct 12 15:09:02 CST 2018
pZxid = 0x70
cversion = 0
dataVersion = 0
aclVersion = 1
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
[zk: localhost:2181(CONNECTED) 3] getAcl /names/test
'digest,'imooc:XwEDaL3J0JQGkRQzM0DpO6zMzZs=
: cdrwa
# 无法获取，需要登录
[zk: localhost:2181(CONNECTED) 4] get /names/test
Authentication is not valid : /names/test
# 登录
[zk: localhost:2181(CONNECTED) 5] addauth digest imooc:imooc
# 可以获取
[zk: localhost:2181(CONNECTED) 6] get /names/test
111
cZxid = 0x70
ctime = Fri Oct 12 15:09:02 CST 2018
mZxid = 0x70
mtime = Fri Oct 12 15:09:02 CST 2018
pZxid = 0x70
cversion = 0
dataVersion = 0
aclVersion = 1
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
```



##### IP 控制



```bash
[zk: localhost:2181(CONNECTED) 17] getAcl /names/ip
'world,'anyone
: cdrwa
# 设置IP可以访问
[zk: localhost:2181(CONNECTED) 18] setAcl /names/ip ip:127.0.0.1:cdrwa
cZxid = 0x73
ctime = Fri Oct 12 15:24:44 CST 2018
mZxid = 0x73
mtime = Fri Oct 12 15:24:44 CST 2018
pZxid = 0x73
cversion = 0
dataVersion = 0
aclVersion = 1
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
[zk: localhost:2181(CONNECTED) 19] getAcl /names/ip
'ip,'127.0.0.1
: cdrwa
```

##### super 

1. 修改zkSever，增加管理员权限
2. 重启 zkServer



mac 下brew 安装的 zkServer的文件目录为：/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/zkServer.sh

加入如下参数：

`"-Dzookeeper.DigestAuthenticationProvider.superDigest=imooc:XwEDaL3J0JQGkRQzM0DpO6zMzZs="`



<img src="/img/post/2018-10-10-zookeeper-1.png" style="width:100%;max-width: 700px;text-align:center;display:block;">



```bash
# 一般不能访问的，用管理员权限登录后，可以访问
[zk: localhost:2181(CONNECTED) 4] addauth digest imooc:imooc
```



#### ACL使用场景

- 开发/测试环境分离。比如开发者无权操作测试库的节点
- 生产环境上控制指定ip的服务可以访问，防止混乱。但是可能的问题：有些节点是动态IP，会需要频繁配置。



### zk四字交互

https://zookeeper.apache.org/doc/r3.4.13/zookeeperAdmin.html#sc_zkCommands



zk通过自身的简写命令来和服务器交互

```bash
# stat 查看状态信息
$ echo stat | nc 127.0.0.1 2181
Zookeeper version: 3.4.12-e5259e437540f349646870ea94dc2658c4e44b3b, built on 03/27/2018 03:55 GMT
Clients:
 /127.0.0.1:65514[0](queued=0,recved=1,sent=0)

Latency min/avg/max: 0/1/64
Received: 84
Sent: 83
Connections: 1
Outstanding: 0
Zxid: 0x79
Mode: standalone
Node count: 10
# ruok(are you ok) 查看zkServer是否启动
$ echo ruok | nc 127.0.0.1 2181
imok%
# dump 列出未经处理的会话和临时节点
$ echo dump | nc 127.0.0.1 2181
SessionTracker dump:
Session Sets (4):
0 expire at Thu Jan 01 11:22:06 CST 1970:
0 expire at Thu Jan 01 11:22:16 CST 1970:
0 expire at Thu Jan 01 11:22:18 CST 1970:
1 expire at Thu Jan 01 11:22:24 CST 1970:
	0x10000a71bce0002
ephemeral nodes dump:
# 临时节点
Sessions with Ephemerals (1):
0x10000a71bce0002:
	/names/tmp
# conf 查看配置
$ echo conf | nc 127.0.0.1 2181
clientPort=2181
dataDir=/usr/local/var/run/zookeeper/data/version-2
dataLogDir=/usr/local/var/run/zookeeper/data/version-2
tickTime=2000
maxClientCnxns=60
minSessionTimeout=4000
maxSessionTimeout=40000
serverId=0
# cons 展示连接的客户端信息
$ echo cons | nc 127.0.0.1 2181
 /0:0:0:0:0:0:0:1:49636[1](queued=0,recved=27,sent=27,sid=0x10000a71bce0002,lop=PING,est=1539332965189,to=30000,lcxid=0x2,lzxid=0x7c,lresp=12292802,llat=0,minlat=0,avglat=0,maxlat=10)
 /127.0.0.1:50693[0](queued=0,recved=1,sent=0) 
# envi 环境变量
$ echo envi | nc 127.0.0.1 2181
Environment:
zookeeper.version=3.4.12-e5259e437540f349646870ea94dc2658c4e44b3b, built on 03/27/2018 03:55 GMT
host.name=133.133.134.93
java.version=1.8.0_121
java.vendor=Oracle Corporation
java.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_121.jdk/Contents/Home/jre
java.class.path=/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/../build/classes:/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/../build/lib/*.jar:/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/../lib/slf4j-log4j12-1.7.25.jar:/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/../lib/slf4j-api-1.7.25.jar:/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/../lib/netty-3.10.6.Final.jar:/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/../lib/log4j-1.2.17.jar:/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/../lib/jline-0.9.94.jar:/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/../lib/audience-annotations-0.5.0.jar:/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/../zookeeper-3.4.12.jar:/usr/local/Cellar/zookeeper/3.4.12/libexec/bin/../src/java/lib/*.jar:/usr/local/etc/zookeeper:
java.library.path=/Users/xushaopeng/Library/Java/Extensions:/Library/Java/Extensions:/Network/Library/Java/Extensions:/System/Library/Java/Extensions:/usr/lib/java:.
java.io.tmpdir=/var/folders/8r/k3y_7kz12xgclstsq4b61nhr0000gn/T/
java.compiler=<NA>
os.name=Mac OS X
os.arch=x86_64
os.version=10.11.6
user.name=xushaopeng
user.home=/Users/xushaopeng
user.dir=/usr/local/Cellar/zookeeper/3.4.12/libexec/bin
# mntr 监控zk健康信息
$ echo mntr | nc 127.0.0.1 2181
zk_version	3.4.12-e5259e437540f349646870ea94dc2658c4e44b3b, built on 03/27/2018 03:55 GMT
zk_avg_latency	0
zk_max_latency	64
zk_min_latency	0
zk_packets_received	129
zk_packets_sent	128
zk_num_alive_connections	2
zk_outstanding_requests	0
zk_server_state	standalone
zk_znode_count	11
zk_watch_count	0
zk_ephemerals_count	1
zk_approximate_data_size	113
zk_open_file_descriptor_count	35
zk_max_file_descriptor_count	10240
# wchs 展示watch信息
$ echo wchs | nc 127.0.0.1 2181
0 connections watching 0 paths
Total watches:0
```



3.4.10版本 后，wchc / wchp命令默认没有开放

修改  `/usr/local/etc/zookeeper/zoo.cfg`，加入`4lw.commands.whitelist=*`，即默认所有的命令都可用。

保存之后需要重启 zkServer ：`zkServer restart`

wchc / wchp 是用于查看session与watch 及 path与watch







