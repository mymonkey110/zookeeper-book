worker的第一步就是通知master它能执行任务。通过在\/workers下面创建临时节点来达到此目的。Worker使用它们的主机名来标示自己：

`[zk: localhost:2181(CONNECTED) 0] create -e /workers/worker1.example.com`

`"worker1.example.com:2224"`

`Created /workers/worker1.example.com`

`[zk: localhost:2181(CONNECTED) 1]`

从输出中可以确认节点已经被创建。回想到master已经在\/workers的孩子节点上设置了监视器。一旦worker在\/workers下面创建了节点，master节点就会观察到以下通知：

`WATCHER::`

`WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/workers`

接下来，worker需要创建一个父节点，\/assign\/worker1.example.com，为的是要接受任务分配。同时，通过执行带参数true的ls命令来监视新的任务：

`[zk: localhost:2181(CONNECTED) 0] create -e /workers/worker1.example.com`

`"worker1.example.com:2224"`

`Created /workers/worker1.example.com`

`[zk: localhost:2181(CONNECTED) 1] create /assign/worker1.example.com ""`

`Created /assign/worker1.example.com`

`[zk: localhost:2181(CONNECTED) 2] ls /assign/worker1.example.com true`

`[]`

`[zk: localhost:2181(CONNECTED) 3]`

worker现在准备好接受任务分配了。当我们讨论客户端的角色时顺带了解一下任务分配。

