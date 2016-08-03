Znode可能包含也可能不包含数据。如果一个znode包含数据，那么数据是以字节数组的形式来存储的。具体字节的组织形式因每个应用而异，并且Zookeeper不提高直接解析字节的方法。序列化的包可以用来处理存储在znode中的数据，例如Protocol Buffers, Thrift, Avro和MessagePack，但有时以UTF-8或者ASCII编码的字符串形式就足够了。

Zookeeper API暴露了下面几个操作：

* create \/path data   创建一个以\/path命名的znode节点，同时保护数据data
* delete \/path 删除路径为\/path的znode节点
* exists \/path 判断是否存储\/path路径的znode节点
* setData \/path data 设置路径为\/path的znode的节点数据为data
* getData \/path 获取路径为\/path的znode节点的数据
* getChildren \/path 获取路径为\/path的znode节点的孩子节点

非常重要的一点是Zookeeper不允许写或者读znode上数据。当设置znode上的数据或者读取时，数据内容是被整个替换或者读取的。


Zookeeper的客户端通过它们发出API调用来连接到Zookeeper服务并且建立一个会话。如果你着急的想使用Zookeeper，请直接跳到“会话”，那一节描述了如何通过一个命令行shell来运行zookeeper的命令。

