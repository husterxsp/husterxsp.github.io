---
layout: post
title: zookeeper的watcher机制
date: 2018-10-10
author: "Xsp"
catalog: true
tags:
    - Zookeeper
    - Java
---



### watcher机制

- 针对每个节点的操作都有一个watcher
- 当监督的某个对象(znode) 变化时，则触发watcher事件
- zk的watcher是一次性的，触发后即销毁

- 父节点、子节点的修改都能触发其watcher



### watcher命令行

- get path [watch] 设置
- 父、子节点的修改触发



### 事件类型

- 创建父节点触发：NodeCreated
- 修改父节点：NodeDatachanged
- 删除父节点：NodeDeleted

```
# 创建
[zk: localhost:2181(CONNECTED) 7] create /imooc 123
Created /imooc
# get的同时设置watch
[zk: localhost:2181(CONNECTED) 8] get /imooc watch
123
cZxid = 0x2c
ctime = Wed Oct 10 09:33:22 CST 2018
mZxid = 0x2c
mtime = Wed Oct 10 09:33:22 CST 2018
pZxid = 0x2c
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
# set，可以看到触发了NodeDataChanged事件
[zk: localhost:2181(CONNECTED) 9] set /imooc 456

WATCHER::

WatchedEvent state:SyncConnected type:NodeDataChanged path:/imooc
cZxid = 0x2c
ctime = Wed Oct 10 09:33:22 CST 2018
mZxid = 0x2d
mtime = Wed Oct 10 09:33:35 CST 2018
pZxid = 0x2c
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
# 删除，因为watch是一次性的，所以删除啥事件也没触发
[zk: localhost:2181(CONNECTED) 10] delete /imooc
# 再次创建
[zk: localhost:2181(CONNECTED) 11] create /imooc asdf
Created /imooc
# 设置watch
[zk: localhost:2181(CONNECTED) 12] get /imooc watch
asdf
cZxid = 0x2f
ctime = Wed Oct 10 09:36:53 CST 2018
mZxid = 0x2f
mtime = Wed Oct 10 09:36:53 CST 2018
pZxid = 0x2f
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 4
numChildren = 0
# 添加watch后直接删除，触发了watch事件
[zk: localhost:2181(CONNECTED) 14] delete /imooc

WATCHER::

WatchedEvent state:SyncConnected type:NodeDeleted path:/imooc
```



- ls 为父节点设置watcher，创建子节点触发 NodeChildrenChanged

- 删除触发NodeChildrenChanged

- 设置，不触发


```
[zk: localhost:2181(CONNECTED) 18] create /imooc 123
Created /imooc
[zk: localhost:2181(CONNECTED) 19] ls /imooc
[]
# 父节点添加watcher
[zk: localhost:2181(CONNECTED) 20] ls /imooc watch
[]
# 创建子节点触发watcher
[zk: localhost:2181(CONNECTED) 21] create /imooc/abc asdf
Created /imooc/abc

WATCHER::
[zk: localhost:2181(CONNECTED) 22]
WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/imooc

[zk: localhost:2181(CONNECTED) 22] ls /imooc
[abc]
[zk: localhost:2181(CONNECTED) 23] ls /imooc watch
[abc]
# 删除触发事件
[zk: localhost:2181(CONNECTED) 24] delete /imooc/abc

WATCHER::

WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/imooc

[zk: localhost:2181(CONNECTED) 0] ls /imooc
[abc]
[zk: localhost:2181(CONNECTED) 1] ls /imooc watch
[abc]
# 设置子节点，不会触发事件。
# 只有对父节点的修改才会触发事件。
[zk: localhost:2181(CONNECTED) 2] set /imooc/abc 123
cZxid = 0x34
ctime = Wed Oct 10 09:49:15 CST 2018
mZxid = 0x37
mtime = Wed Oct 10 09:50:17 CST 2018
pZxid = 0x34
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
```

### watch使用场景

- 统一资源配置

  - 节点设置watch，修改后触发，然后通知客户端修改新的配置信息


