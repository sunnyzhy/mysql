数据库构架设计中主要有Shared Everthting、Shared Nothing、和Shared Disk：

- **Shared Everthting:** 一般是针对单个主机，完全透明共享 CPU/MEMORY/Disk，并行处理能力是最差的。

- **Shared Disk:** 各个节点都有自己的私有 CPU/Memory，共享磁盘系统。典型的代表 Oracle Rac， 它是数据共享，可通过增加节点来提高并行处理的能力，扩展能力较好。其类似于 SMP（对称多处理）模式，但是当存储器接口达到饱和的时候，增加节点并不能获得更高的性能。

- **Shared Nothing:** 各个节点都有自己私有的 CPU/Memory/Disk 等，不存在共享资源，类似于MPP（大规模并行处理）模式，各处理单元之间通过协议通信，并行处理和扩展能力更好。典型代表 DB2 DPF 和 hadoop ，各节点相互独立，各自处理自己的数据，处理后的结果可能向上层汇总或在节点间流转。

我们常说的 Sharding 其实就是 Share Nothing 架构，它是把某个表从物理存储上被水平分割，并分配给多台服务器（或多个实例），每台服务器可以独立工作，具备共同的 schema，比如 MySQL Proxy 和 Google 的各种架构，只需增加服务器数就可以增加处理能力和容量。
