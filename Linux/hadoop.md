# Hadoop

### 组成
1. Hadoop HDFS 类型与磁盘,一个高可靠,高吞吐量的分布式文件系统
    * NameNode(nn),存储元数据,类似于目录,存储文件名，文件目录结构,文件属性
    * DataNode(dn),在本地文件系统存储文件块数据,以及数据的校验和
    * secondary NameNode(2nn),用来监控HDFS状态的辅助后台程序,每隔一段时间获取HDFS元数据的快照
2. Hadoop MapReduce 一个分布式离线并行计算框架
    * MapReduce 分为两个阶段,map每个节点处理一个Job不同的地方,Reduce把这些整合起来
3. Hadoop YARN 作业调度集群资源管理的框架
    * ResourceManager 处理客户端请求、启动/监控 ApplicationMaster、监控 NodeManager、资源分配与调度
    * NodeManager 单个节点上的资源管理、处理来自 ResourceManager 的命令、处理来自 ApplicationMaster 的命令
    * ApplicationMaster 数据切分、为应用程序申请资源，并分配给内部任务、任务监控与容错。
    * Container 对任务运行环境的抽象，封装了 CPU、内存等多维资源以及环境变量、启动命令等任务运行相关的信息。
4. Hadop Common 支持其他模块的工具模块
