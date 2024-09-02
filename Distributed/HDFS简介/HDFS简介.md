
# HDFS简介与特点
HDFS即Hadoop Distributed File System，是一个分布在多个集群节点上的文件系统，节点之间通过网络通信进行协作，提供多个节点的文件信息。

在设计上，HDFS的目标是能在普通机器上以极高的吞吐量读写非常大的文件。对此，HDFS中的文件将被拆分成多个数据块（block）存储在不同的节点上。Block大小一般默认为128MB，然而，对于某个小于128MB的文件（假设该文件10MB），它虽然也会占用一个block，但这个block实际上只占用磁盘10MB空间，不会填充为128MB。

文件被存储到一至多个block中，每个block会被复制到多个DataNode（默认3个），以确保数据的可靠性。

实际上，block下面还有更小的存储单位packet，packet下还有chunk。

<br/><br/>

# 为什么block的大小被设置在128MB

简单来说，block越大，寻址时间越短，但磁盘传输时间越长。

如果block过大，从磁盘传输数据的时间会明显大于寻址时间，程序在处理block时，等待磁盘IO的时间以及处理block本身的时间就太长了，效率不高。

如果block过小，在文件总量不变的情况下，block数量就会变多，NameNode就需要更多的资源存储元数据。而且block过多，寻址时间也会变长。

综上，block的大小不能过大也不能过小，而且根据测试发现，block的寻址时间最好等于block传输时间的1%。由于block寻址时间一般在0.01s，当磁盘传输速度为100MB/s，100MB的block传输时间就为1s，传输时间的1%正好等于0.01s。因此block大小被设置为128MB。当然这只是个默认值，在HDFS中可以根据实际磁盘传输速度而更改。

由于block大小设置为一个折中的值，因此HDFS无法以极低的延迟访问文件，大量（数以亿计）小文件的存储在HDFS上的效果也不好。

<br/><br/>


# HDFS架构

整个HDFS集群由NameNode和DataNode构成master-worker（主从）模式。NameNode负责构建命名空间，管理文件的元数据（如文件名、权限、block位置等），而DataNode负责实际的数据存储与读写工作，并定期向NameNode发送心跳信号，报告其健康状态和存储情况。

除了NameNode和DataNode，还有一个特殊的Secondary NameNode，它定期获取NameNode的元数据快照并合并编辑日志，以减轻NameNode的负载。

<br/><br/>


# 文件的存储与备份

当客户端上传文件到HDFS时，NameNode负责给新文件分配block，并根据每个DataNode的情况把新的block安排到指定的DataNode，然后客户端根据分配情况将文件分块并写入到指定DataNode中。

当客户端需要从HDFS下载文件时，NameNode查找文件的元信息，并给出文件block的具体位置，客户端根据位置读取指定的DataNode，然后将读取到的多个block组合起来形成完整文件。

<br/><br/>


# HDFS命令
## hdfs dfs
用于管理HDFS文件系统中的目录和文件。
```bash
# 列出HDFS指定目录下的文件和目录
hdfs dfs -ls 路径 
 
# 在HDFS中创建一个新目录
hdfs dfs -mkdir 路径
 
#将本地文件上传到HDFS
hdfs dfs -put 本地路径 路径
 
# 将HDFS中的文件下载到本地
hdfs dfs -get 路径 本地路径
 
# 删除HDFS中的文件或目录
hdfs dfs -rm 路径
 
# 移动或重命名HDFS文件或目录
hdfs dfs -mv 原路径 目标路径

# 显示HDFS中指定文件
hdfs dfs -cat 路径
```
## hdfs fsck
用于检查HDFS中文件和目录的block信息（包括block大小、位置、健康状况、备份情况等）。
```bash
hdfs fsck 路径 -files -blocks -locations
```

<br/><br/>

# 参考资料

[一篇讲懂为什么HDFS文件块（block）大小设定为128M](https://blog.csdn.net/wx1528159409/article/details/84260023)
