因为只有一个进程能成为master，进程必须在zookeeper锁住领导权才能成为master。要达到此目的，进程需要创建一个名为\/master的临时节点：

`[zk: localhost:2181(CONNECTED) 0] create -e /master "master1.example.com:2223"  <1>`

`Created /master`

`[zk: localhost:2181(CONNECTED) 1] ls /  <2>`

`[master, zookeeper]`

`[zk: localhost:2181(CONNECTED) 2] get /master <3>`

`"master1.example.com:2223"`

`cZxid = 0x67 `

`ctime = Tue Dec 11 10:06:19 CET 2012`

`mZxid = 0x67`

`mtime = Tue Dec 11 10:06:19 CET 2012`

`pZxid = 0x67`

`cversion = 0`

`dataVersion = 0`

`aclVersion = 0`

`ephemeralOwner = 0x13b891d4c9e0005`

`dataLength = 26`

`numChildren = 0`

`[zk: localhost:2181(CONNECTED) 3]`

1. 创建一个master节点得到领导权。我们使用-e标志表明我们创建的临时节点。
2. 列出zookeeper树的根节点。
3. 得到\/master节点的元数据和内容数据。

刚刚发生了什么？我们先创建了一个临时节点\/master。以防其他人需要在Zookeeper外和它交互，我们在节点上加上了主机的信息。加上主机信息并不是必须的，这么做只是演示一下我们可以这么做。为了让节点成为临时节点，我们加上了-e标志。记住临时节点会在创建它的会话关闭或者过期时自动地删除。

假如现在我们有两个进程竞争领导master的角色，但是在任意时刻最多只能有一个激活的master。其他的进程是备用的master。假设其他进程不知道master已经被选举出来了，它们也尝试创建\/master节点。我们看看发生了什么：

`[zk: localhost:2181(CONNECTED) 0] create -e /master "master2.example.com:2223"`

`Node already exists: /master`

`[zk: localhost:2181(CONNECTED) 1]`

Zookeeper告诉我们\/master节点已经存在了。以这种方式第二个进程就知道了已经有master了。然而，那个激活的master可能奔溃，后备的master需要接管激活master的角色。为了检测到这种情况，我们需要在\/master节点上按照下面的方式设置一个监视器：

`[zk: localhost:2181(CONNECTED) 0] create -e /master "master2.example.com:2223"`

`Node already exists: /master`

`[zk: localhost:2181(CONNECTED) 1] stat /master true`

`cZxid = 0x67`

`ctime = Tue Dec 11 10:06:19 CET 2012`

`mZxid = 0x67`

`mtime = Tue Dec 11 10:06:19 CET 2012`

`pZxid = 0x67`

`cversion = 0`

`dataVersion = 0`

`aclVersion = 0`

`ephemeralOwner = 0x13b891d4c9e0005`

`dataLength = 26`

`numChildren = 0`

`[zk: localhost:2181(CONNECTED) 2]`

stat命令能够获得一个节点的属性，它允许我们在一个存在的节点上设置监视器。在路径后面设置参数为true来设置监视器。在本例中，当那个活跃的主master奔溃时，我们观察到了以下信息：

`[zk: localhost:2181(CONNECTED) 0] create -e /master "master2.example.com:2223"`

`Node already exists: /master`

`[zk: localhost:2181(CONNECTED) 1] stat /master true`

`cZxid = 0x67`

`ctime = Tue Dec 11 10:06:19 CET 2012`

`mZxid = 0x67`

`mtime = Tue Dec 11 10:06:19 CET 2012`

`pZxid = 0x67`

`cversion = 0`

`dataVersion = 0`

`aclVersion = 0`

`ephemeralOwner = 0x13b891d4c9e0005`

`dataLength = 26`

`numChildren = 0`

`[zk: localhost:2181(CONNECTED) 2]`

`WATCHER::`

`WatchedEvent state:SyncConnected type:NodeDeleted path:/master`

`[zk: localhost:2181(CONNECTED) 2] ls /`

`[zookeeper]`

`[zk: localhost:2181(CONNECTED) 3]`

注意在最后输出的NodeDeleted事件。该事件表明那个主服务器的会话关闭了，要么就是过期了。注意到\/master节点不再存在了。备用的主服务器现在应该尝试通过创建\/master节点来变为主服务器。

`[zk: localhost:2181(CONNECTED) 0] create -e /master "master2.example.com:2223"`

`Node already exists: /master`

`[zk: localhost:2181(CONNECTED) 1] stat /master true`

`cZxid = 0x67`

`ctime = Tue Dec 11 10:06:19 CET 2012`

`mZxid = 0x67`

`mtime = Tue Dec 11 10:06:19 CET 2012`

`pZxid = 0x67`

`cversion = 0`

`dataVersion = 0`

`aclVersion = 0`

`ephemeralOwner = 0x13b891d4c9e0005`

`dataLength = 26`

`numChildren = 0`

`[zk: localhost:2181(CONNECTED) 2]`

`WATCHER::`

`WatchedEvent state:SyncConnected type:NodeDeleted path:/master`

`[zk: localhost:2181(CONNECTED) 2] ls /`

`[zookeeper]`

`[zk: localhost:2181(CONNECTED) 3] create -e /master "master2.example.com:2223"`

`Created /master`

`[zk: localhost:2181(CONNECTED) 4]`

因为它成功地创建了\/master节点，现在客户端就变成了激活的master。

