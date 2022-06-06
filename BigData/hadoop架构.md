# hadoop架构

主要组件Hadoop:HDFS/YARN/MapReduce

## hdfs:目标

(master/slave arch) 

检测故障并从中快速自动恢复是 HDFS 的核心架构目标 

对其数据集进行流式访问

数据访问的高吞吐量，而不是数据访问的低延迟

将计算迁移到更靠近数据所在的位置通常比将数据移动到应用程序运行的位置更好



HDFS 通信协议都建立在 TCP/IP 协议之上

HDFS 提供了一个[FileSystem Java API](http://hadoop.apache.org/docs/current/api/)供应用程序使用。此 Java API和[REST API](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/WebHDFS.html)的[C 语言包装器](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/LibHdfs.html)也可用。此外，HTTP 浏览器也可用于浏览 HDFS 实例的文件。通过使用[NFS 网关](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsNfsGateway.html)，可以将 HDFS 作为客户端本地文件系统的一部分进行挂载。

## 架构

​	NameNode 一个 Namespace 客户端访问 单点问题（singe point of failure）——》HA 

​							metadata：创建者 权限 文件对应的block信息 

​							用户数据永远不会流经 NameNode

​							在启动时，NameNode 会进入安全模式，此时不会发生数据块的复制

​	DataNodes 多个 数据存储 和NameNode有心跳信息 

​	SecondaryNameNode

<img src="C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220329231205044.png" alt="image-20220329231205044" style="zoom: 50%;" />

Rack 基架 | 绿色block 128MB | 



# YARN架构

Yet Another Resource Negotiator另一种资源管理。主从架构

ResourceManager和NodeManager构成了计算机数据框架，

ResourceManager是在系统中的所有应用程序中仲裁资源的最终权威，

NodeManager是每台机器框架代理负责容器,监测他们的资源使用(cpu、内存、磁盘、网络)和报告同一个的ResourceManager /Scheduler调度程序。

![YARN架构](https://img-blog.csdn.net/20170802145535483?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzg3NzY2NTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



# HDFS读写流程

Client 请求读写操作

NameNode 协调工作 元数据管理

DataNode 数据存储

## 写

配置文件

​	1.blocksize 128MB默认

​	2.replicas 副本

过程

​	代码：上传文件

​	client：拆分file

​	client to NN: 创造replicas

​	NN to client ：DN1，DN2，DN3

​	client to DN[i]: 写入

​	DN to NN：元数据信息更新



## 读

过程

​	代码：读取命令

​	client to NN：请求元数据信息

​	NN to client: 获取元数据位置信息

​	client to DN：获取最近DN数据

<img src="C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220331105230758.png" alt="image-20220331105230758" style="zoom: 80%;" />



# HA架构(High Availability)

生产上的东西一定要保证高可用

![HA架构](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220330142012964.png)

2个NN 

Active 平时使用的

Standby 同步Active

FailoverController两个分别监控Active和Standby



resource manager HA起不来

namenode manager HA起不来



# 小文件问题

定义 明显小于block size的文件

129M：128+1

Hadoop中NN的小文件的目录、文件、block一条大概200字节

性能会急剧下降

性能瓶颈

1）小文件导致磁盘IO时间过长

2）task启动销毁的开销

3）资源有限 map多，节点有限



# SQL On hadoop框架

<img src="C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220331151548427.png" alt="image-20220331151548427" style="zoom:50%;" />

Hive：sql 转化为 对应的执行引擎的作业 MapReduce/Spark/Tez

Impala：内存需求很大

Presto，

Drill，

Phoenix:查询HBase，HBase基于rowkey存储

Spark SQL：



MetaStore：存储元数据信息 框架之间共享元数据信息



# 行式存储/列式存储

行式存储倾向于结构固定，列式存储倾向于结构弱化。

行式存储一行数据只需一份主键，列式存储一行数据需要多份主键。

行式存储存的都是业务数据，列式存储除了业务数据外，还要存储列名。

行式存储更像一个Java Bean，所有字段都提前定义好，且不能改变；列式存储更像一个Map，不提前定义，随意往里添加key/value。

## 行式存储适用场景

1. 适合随机的增删改查操作
2. 需要在行中选取所有属性的查询操作
3. 需要频繁插入或更新的操作，其操作与索引和行的大小更为相关

## 列式存储适用场景

1. 查询过程中，可针对各列的运算并发执行(SMP)，最后在内存中聚合完整记录集，最大可能降低查询响应时间;

2. 可在数据列中高效查找数据，无需维护索引(任何列都能作为索引)，查询过程中能够尽量减少无关IO，避免全表扫描;

3. 因为各列独立存储，且数据类型已知，可以针对该列的数据类型、数据量大小等因素动态选择压缩算法，以提高物理存储利用率;如果某一行的某一列没有数据，那在列存储时，就可以不存储该列的值，这将比行式存储更节省空间。

   

# 调优策略

在资源不变的情况下,让作业的执行性能有提升

降低 CPU负载 或 IO负载

## 架构

### 分表

流程：flume -> hdfs -> spark ETL -> spark sql -> sql -> spark sql/Nosql

前提：

1.行式

2.每分钟数据很多

3.每天有500个作业访问大表

将某个作业访问的字段从大表中抽出来 重新建表

### 分区表 partition

例如系统用户日志

单级/多级分区，静态/动态分区

### 充分利用中间结果集

![image-20220331155611596](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220331155611596.png)

### 压缩

使用压缩算法来‘’减少‘’数据的过程

![image-20220331160034251](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220331160034251.png)

![image-20220331160129835](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220331160129835.png)

使用场景：输入数据|中间数据|输出数据

充分考虑压缩比与压缩/解压速度

![image-20220401174211818](C:\Users\12432\AppData\Roaming\Typora\typora-user-images\image-20220401174211818.png)



## 语法

### 排序

order by /sort by/distribute by/cluster by

严格模式order by一定要limit，order by只有一个reducer 全局排序

sort by只能保证每个reduce内部是排序的,并不能保证全局有序

distribute by 根据某个字段进行分发到不同reduce中

 当distribute by 和 sort by 所指定的字段相同时，即可以使用cluster by。
   注意:cluster by指定的列只能是降序，不能指定asc和desc。

### 控制输出的数量

决定输出文件个数 

每个reduce默认 256mb（0.14.0之前是1G）

reduce个数模型 1009（0.14.0之前是999）

N = min(参数1，输入数据总数量/参数2)

reducer数量很大：小文件出现次数很多

reducer数量很小：耗时过长，执行不出来

### join:普通join/mapjoin

 join mapreduce实现思路：

1.mapper读取a表，mapper读取b表

2.数据结构（a.id,a.x）(b.id,b.x)

3.shuffle相同的key分发到相同的reducer去

4.在reduce端完成真正的join操作 

普通join需要经过shuffle

mapjoin：没有shuffle 也叫做board cast join

1.把小表加载缓存（需要缓存较大）

2.通过mapper读取大表数据

3.在读取大表数据记录时候与缓存中的小表直接进行对比

4.join上就ok



### 执行计划

explain

## 执行

推测执行

并行执行

JVM重用







