我们已经抽象的讨论过了分布式系统，现在是时候给出让它更加具体了。让我们考虑一个在分布式系统中十分常用的架构：master-work架构。这种架构的一个重要例子就是HBase,一个Google的Bigtable的克隆版。从高层来看，master服务器\(HMaster\)负责追踪区域服务器\(HRegionServer\)的可用性并且指派区域给服务器。由于我们这里无法完整的介绍它，我们鼓励你查看HBase的文档以进一步阅读它是如何使用Zookeeper的细节的。我们的讨论只关注一个通用的master-worker架构。

![](/assets/Figure 1-1.png)

Figure 1-1. Master-worker example

通常，这种架构master进程负责追踪workers和任务的可用性，还要指派任务给workers。对Zookeeper来说，这种架构风格是很有代表性的，因为它阐述了很多常用的任务，比如选举master，追踪可用的worker，维护应用数据。

为了实现一个master-worker系统，我们必须解决下面三个关键的问题：

### Master奔溃

如果Master发生错误变得不可用，系统不能分配新的任务或者重新分配那些来自worker已经失败的任务。

### Worker奔溃

如果Worker奔溃，那些已经指定给它的任务就不会完成。

### Communication失败

如果master和worker不能交换信息，那么worker可能就不能得到新的分配给它的任务。



