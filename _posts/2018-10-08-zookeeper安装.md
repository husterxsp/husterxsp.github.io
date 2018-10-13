---
layout: post
title: Mac 安装 zookeeper
date: 2018-10-08
author: "Xsp"
catalog: true
tags:
    - Zookeeper
    - Java
---



### brew安装

```bash
$ brew install zookeeper
```

因为之前装过，这里再重新装一下：

```
$ brew reinstall zookeeper
==> Downloading https://homebrew.bintray.com/bottles/zookeeper-3.4.12.el_capitan.bottle.tar.gz
######################################################################## 100.0%
==> Pouring zookeeper-3.4.12.el_capitan.bottle.tar.gz
==> Caveats
To have launchd start zookeeper now and restart at login:
  brew services start zookeeper
Or, if you don't want/need a background service you can just run:
  zkServer start
==> Summary
🍺  /usr/local/Cellar/zookeeper/3.4.12: 242 files, 32.9MB
```

查看zookeeper的信息：

```bash
$ brew info zookeeper
zookeeper: stable 3.4.12 (bottled), HEAD
Centralized server for distributed coordination of services
https://zookeeper.apache.org/
/usr/local/Cellar/zookeeper/3.4.12 (242 files, 32.9MB) *
  Poured from bottle on 2018-10-08 at 19:57:49
From: https://github.com/Homebrew/homebrew-core/blob/master/Formula/zookeeper.rb
==> Options
--HEAD
	Install HEAD version
==> Caveats
To have launchd start zookeeper now and restart at login:
  brew services start zookeeper
Or, if you don't want/need a background service you can just run:
  zkServer start
==> Analytics
install: 3,550 (30d), 13,807 (90d), 46,006 (365d)
install_on_request: 1,202 (30d), 4,955 (90d), 15,363 (365d)
build_error: 0 (30d)
```

安装完成，配置文件在 `/usr/local/etc/zookeeper/` ，

```bash
$ ls /usr/local/etc/zookeeper
defaults         log4j.properties zoo.cfg          zoo_sample.cfg
```

其中我们一般需要修改的是 `zoo.cfg` 文件。bash

```bash
$ less -N /usr/local/etc/zookeeper/zoo.cfg
      1 # The number of milliseconds of each tick
      2 tickTime=2000
      3 # The number of ticks that the initial
      4 # synchronization phase can take
      5 initLimit=10
      6 # The number of ticks that can pass between
      7 # sending a request and getting an acknowledgement
      8 syncLimit=5
      9 # the directory where the snapshot is stored.
     10 # do not use /tmp for storage, /tmp here is just
     11 # example sakes.
     12 dataDir=/usr/local/var/run/zookeeper/data
     13 # the port at which the clients will connect
     14 clientPort=2181
     15 # the maximum number of client connections.
     16 # increase this if you need to handle more clients
     17 #maxClientCnxns=60
     18 #
     19 # Be sure to read the maintenance section of the
     20 # administrator guide before turning on autopurge.
     21 #
     22 # http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
     23 #
     24 # The number of snapshots to retain in dataDir
     25 #autopurge.snapRetainCount=3
     26 # Purge task interval in hours
     27 # Set to "0" to disable auto purge feature
     28 #autopurge.purgeInterval=1
```



启动：

```
# 查看帮助
$ zkServer
ZooKeeper JMX enabled by default
Using config: /usr/local/etc/zookeeper/zoo.cfg
Usage: ./zkServer.sh {start|start-foreground|stop|restart|status|upgrade|print-cmd}
# 启动
$ zkServer start
ZooKeeper JMX enabled by default
Using config: /usr/local/etc/zookeeper/zoo.cfg
Starting zookeeper ... already running as process 10236.
# 查看状态
$ zkServer status
ZooKeeper JMX enabled by default
Using config: /usr/local/etc/zookeeper/zoo.cfg
# standalone 即单机模式
Mode: standalone
```

如果需要后台启动：

```bash
$ brew services start zookeeper
```



查看有哪些数据节点，zkCli 工具：

```bash
$ zkCli                                                                                                                                              
Connecting to localhost:2181
Welcome to ZooKeeper!
JLine support is enabled

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
[zk: localhost:2181(CONNECTED) 1] help
ZooKeeper -server host:port cmd args
	stat path [watch]
	set path data [version]
	ls path [watch]
	delquota [-n|-b] path
	ls2 path [watch]
	setAcl path acl
	setquota -n|-b val path
	history
	redo cmdno
	printwatches on|off
	delete path [version]
	sync path
	listquota path
	rmr path
	get path [watch]
	create [-s] [-e] path data acl
	addauth scheme auth
	quit
	getAcl path
	close
	connect host:port
[zk: localhost:2181(CONNECTED) 0] ls /
[zookeeper]
[zk: localhost:2181(CONNECTED) 1] ls /zookeeper
[quota]
[zk: localhost:2181(CONNECTED) 2] ls /zookeeper/quota
[]
[zk: localhost:2181(CONNECTED) 3] quit
Quitting...
```



### 源代码安装



官网直接download：https://zookeeper.apache.org/releases.html

选择一个镜像源下载，这里随便选一个：https://mirrors.koehn.com/apache/zookeeper/stable/

解压后，文件目录如下：

```bash
$ ll
total 3184
-rw-r--r--@  1 xushaopeng  staff    12K  3 27  2018 LICENSE.txt
-rw-r--r--@  1 xushaopeng  staff   3.1K  3 27  2018 NOTICE.txt
-rw-r--r--@  1 xushaopeng  staff   1.5K  3 27  2018 README.md
-rw-r--r--@  1 xushaopeng  staff   1.7K  3 27  2018 README_packaging.txt
drwxr-xr-x@ 10 xushaopeng  staff   340B  3 27  2018 bin
-rw-r--r--@  1 xushaopeng  staff    86K  3 27  2018 build.xml
drwxr-xr-x@  5 xushaopeng  staff   170B  3 27  2018 conf
drwxr-xr-x@ 10 xushaopeng  staff   340B  3 27  2018 contrib
drwxr-xr-x@ 32 xushaopeng  staff   1.1K  3 27  2018 dist-maven
drwxr-xr-x@ 49 xushaopeng  staff   1.6K  3 27  2018 docs
-rw-r--r--@  1 xushaopeng  staff   8.0K  3 27  2018 ivy.xml
-rw-r--r--@  1 xushaopeng  staff   1.7K  3 27  2018 ivysettings.xml
drwxr-xr-x@ 13 xushaopeng  staff   442B  3 27  2018 lib
drwxr-xr-x@  5 xushaopeng  staff   170B  3 27  2018 recipes
drwxr-xr-x@ 14 xushaopeng  staff   476B  3 27  2018 src
-rw-r--r--@  1 xushaopeng  staff   1.4M  3 27  2018 zookeeper-3.4.12.jar
-rw-r--r--@  1 xushaopeng  staff   819B  3 27  2018 zookeeper-3.4.12.jar.asc
-rw-r--r--@  1 xushaopeng  staff    33B  3 27  2018 zookeeper-3.4.12.jar.md5
-rw-r--r--@  1 xushaopeng  staff    41B  3 27  2018 zookeeper-3.4.12.jar.sha1
```

主要文件夹：

- bin：可执行文件
- conf：配置文件
  - zoo_sample.cfg：需要我们设置
- contrib：附加的一些功能
- dist-maven：mvn编译后的目录
- docs：离线文档
- lib：依赖的jar包
- recipes：案例demo
- src：源码



### zoo.cfg 配置

如果用上述源码的方式安装，conf 目录里没有 zoo.cfg，可以复制一份 zoo_sample.cfg，然后改名为 zoo.cfg



下面用 `/usr/local/etc/zookeeper/zoo.cfg`

```bash
# The number of milliseconds of each tick
# 用于计算的时间单元，下面的其它时间都是以这个tick为单位的
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
# 10个tick时间。用于集群，允许从节点连接并同步到master节点的初始化连接时间
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
# 主从节点发消息，请求和应答时间长度
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
# 数据存储的目录，必须配置
# 还有 daraLogDir, 日志目录，如果不配，和 dataDir 共用
dataDir=/usr/local/var/run/zookeeper/data
# the port at which the clients will connect
# 连接服务器的端口，默认2181。如果是伪分布式，即在单台部署多个zookeeper节点，那么可以改这个端口实现
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
```





### 参考

- [https://blog.csdn.net/whereismatrix/article/details/50420099](https://blog.csdn.net/whereismatrix/article/details/50420099)
