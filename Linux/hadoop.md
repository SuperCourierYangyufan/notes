# Hadoop

### 组成
1. Hadoop HDFS 类型与磁盘,一个高可靠,高吞吐量的分布式文件系统
    * NameNode(nn),存储元数据,类似于目录,存储文件名，文件目录结构,文件属性
    * DataNode(dn),在本地文件系统存储文件块数据,以及数据的校验和
    * secondary NameNode(2nn),用来监控HDFS状态的辅助后台程序,每隔一段时间获取HDFS元数据的快照
2. Hadoop MapReduce 一个分布式离线并行计算框架
3. Hadoop YARN 作业调度集群资源管理的框架
4. Hadop Common 支持其他模块的工具模块
