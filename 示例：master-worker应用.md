我们已经抽象的讨论过了分布式系统，现在是时候给出让它更加具体了。让我们考虑一个在分布式系统中十分常用的架构：master-work架构。这种架构的一个重要例子就是HBase,一个Google的Bigtable的克隆版。从高层来看，master服务器\(HMaster\)负责追踪区域服务器\(HRegionServer\)的可用性并且指派区域给服务器。由于我们这里无法完整的介绍它，我们鼓励你查看HBase的文档以进一步阅读它是如何使用Zookeeper的细节的。我们的讨论只关注一个通用的master-worker架构。

![](/assets/Figure 1-1.png)



