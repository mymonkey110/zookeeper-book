让我们在本地以单机模式建立一个Zookeeper并创建一个会话。要达到此目的，我们使用伴随Zookeeper发行版在_bin_目录下的zkServer和zkCli工具。有经验的管理员会使用它们调试和管理，但现在它也非常适合初学者熟悉Zookeeper。

假设你下载并解压了一个Zookeeper的发行版，打开一个shell，切换目录\(cd\)到工程的根目录，重命名示例配置文件：

`#mv conf/zoo_sample.cfg conf/zoo.cfg`

虽然是可选的，但最好把data目录从\/tmp移出避免zookeeper填满你的根分区。你在zoo.cfg中可以改变它的位置：

`dataDir=/users/me/zookeeper`

最后启动服务器，执行以下命令：

`# bin/zkServer.sh start`

`JMX enabled by default`

`Using config: ../conf/zoo.cfg`

`Starting zookeeper ... STARTED`

`#`

这条服务器命令使得Zookeeper服务器在后台运行起来。如果要在前台运行使得我们能看到服务器的输出，你可以运行下面的命令：

`#bin/zkServer.sh start-foreground`

这个选项让你有更加详细的输出，允许你看到服务器正在发生什么。

我们现在准备启动一个客户端。在工程的根目录下另起一个不同的shell，运行下面的命令：

`#bin/zkCli.sh`

`.`

`.`

`.`

`<some omitted output>`

`.`

`.`

`.`

`2012-12-06 12:07:23,545 [myid:] - INFO [main:ZooKeeper@438] - (1)`

`Initiating client connection, connectString=localhost:2181`

`sessionTimeout=30000 watcher=org.apache.zookeeper.`

`ZooKeeperMain$MyWatcher@2c641e9a`

`Welcome to ZooKeeper!`

`2012-12-06 12:07:23,702 [myid:] - INFO [main-SendThread  - (2)`

`(localhost:2181):ClientCnxn$SendThread@966] - Opening`

`socket connection to server localhost/127.0.0.1:2181.`

`Will not attempt to authenticate using SASL (Unable to`

`locate a login configuration)`

`JLine support is enabled`

`2012-12-06 12:07:23,717 [myid:] - INFO [main-SendThread  - (3)`

`(localhost:2181):ClientCnxn$SendThread@849] - Socket`

`connection established to localhost/127.0.0.1:2181, initiating`

`session [zk: localhost:2181(CONNECTING) 0]`

`2012-12-06 12:07:23,987 [myid:] - INFO [main-SendThread - (4)`

`(localhost:2181):ClientCnxn$SendThread@1207] - Session`

`establishment complete on server localhost/127.0.0.1:2181,`

`sessionid = 0x13b6fe376cd0000, negotiated timeout = 30000`

`WATCHER::`

`WatchedEvent state:SyncConnected type:None path:null - (5)`

1. 客户端启动建立会话的过程。
2. 客户端尝试连接localhost\/127.0.0.1:2181
3. 客户端连接成功，服务器开始初始化一个新的连接
4. 会话成功地初始化完成
5. 服务器发生SyncConnected事件给客户端

我们来看一下输出。有很多行告诉我们不同的环境变量是如何设置的以及客户端使用的什么Jar包。在这个例子中我们忽略他们，关注在会话的建立上，多花点时间分析你屏幕上的输出信息。

在输出的最后，我们看到了一些涉及到会话建立的日志。第一个是说“初始化连接”。这个消息表述了正在发生的事情，但是一个重要的附加信息是说它正在尝试连接到一台服务器，连接地址是客户端发出的localhost\/127.0.0.1:2181。这个例子中，连接串只包含了localhost，所以这就是将要建立的一个连接。接下来我们看到一条关于SASL的消息，我们先忽略这条消息。紧接着是一条关于客户端与本地Zookeeper服务器建立TCP连接的消息。最后一条日志消息确认了连接已经建立，同时告诉我们连接的ID：0x13b6fe376cd0000。最后，客户端库以一个SyncConnected事件通知应用程序。应用应该事先Watcher对象来处理改事件。我们在下一节再讲更多的事件。

刚刚对Zookeeper有了更多的熟悉，让我们列出根路径下面的znode节点并创建一个znode。让我们首先确认数据树此时是空的，除了\/zookeeper节点，该节点标记了Zookeeper服务维护的元数据树：

`WATCHER::`

`WatchedEvent state:SyncConnected type:None path:null`

`[zk: localhost:2181(CONNECTED) 0] ls /`

`[zookeeper]`

这里发生了什么？我们执行了ls \/发现只有\/zookeeper。现在我们创建一个叫\/workers的节点并确认真的存在：

`WATCHER::`

`WatchedEvent state:SyncConnected type:None path:null`

`[zk: localhost:2181(CONNECTED) 0]`

`[zk: localhost:2181(CONNECTED) 0] ls /`

`[zookeeper]`

`[zk: localhost:2181(CONNECTED) 1] create /workers ""`

`Created /workers`

`[zk: localhost:2181(CONNECTED) 2] ls /`

`[workers, zookeeper]`

`[zk: localhost:2181(CONNECTED) 3]`

为了完成这个联系，我们删除znode并退出：

`[zk: localhost:2181(CONNECTED) 3] delete /workers`

`[zk: localhost:2181(CONNECTED) 4] ls /`

`[zookeeper]`

`[zk: localhost:2181(CONNECTED) 5] quit`

`Quitting...`

`2012-12-06 12:28:18,200 [myid:] - INFO [main-EventThread:ClientCnxn$`

`EventThread@509] - EventThread shut down`

`2012-12-06 12:28:18,200 [myid:] - INFO [main:ZooKeeper@684] - Session:`

`0x13b6fe376cd0000 closed`

观察到\/workers节点已经被删除了，同时会话已经被关闭了。为了清理干净，让我们关闭Zookeeper服务器：

`# bin/zkServer.sh stop`

`JMX enabled by default`

`Using config: ../conf/zoo.cfg`

`Stopping zookeeper ... STOPPED`

`#`



