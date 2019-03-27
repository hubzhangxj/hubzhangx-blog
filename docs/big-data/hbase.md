# Apache Hbase
[Hbase官网指导](http://hbase.apache.org/book.html#arch.overview)  
[Hbase下载链接](http://archive.apache.org/dist/hbase/)  

## Hbase配置
>这里以Apache Hbase-1.2.2版本为例进行说明,其配置文件如下:
>>hadoop-metrics2-hbase.properties  
>>hbase-env.cmd  
>>hbase-env.sh  
>>hbase-policy.xml    
>>hbase-site.xml  
>>log4j.properties   
>>regionservers  

### 1.hbase-site.xml
```xml
<configuration>
  //配置hdfs存放路径
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://master:9000/hbase</value>
  </property>

  //开启集群
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>

  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>

  <property>
    <name>hbase.tmp.dir</narme>
    <value>/home/basetmp</value>
  </property>

  <property>
    <name>hbase.master</name>
    <value>192.168.0.10:60000</value>
  </property>
  //配置zookeeper服务器，默认端口号，2181可以不用写
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>node-a.example.com,node-b.example.com,node-c.example.com</value>
  </property>
</configuration>
```
**hbase.zookeeper.quorum的个数必须是奇数**  
### 2.hbase-env.sh  
```shell-session
export HBASE_OPTS="$HBASE_OPTS -XX:+HeapDumpOnOutOfMemoryError -XX:+UseConcMarkSweepGC -XX:+CMSIncrementalMode"
export JAVA_HOME=/usr/lib/jvm/javapkg/jdk8u-server-release-1804
export HBASE_CLASSPATH=/usr/lib/jvm/javapkg/jdk8u-server-release-1804/jre
export HBASE_HOME=/root/app/hbase-1.2.0-cdh5.7.0
export HBASE_LOG_DIR=${HBASE_HOME}/logs
export HBASE_MANAGES_ZK=false
```
HBASE_MANAGES_ZK环境变量是使用自带Zookeeper还是使用独立Zookeeper  
设置为true时,使用默认自带的  
设置为false时,使用独立的  

-XX:+HeapDumpOnOutOfMemoryError 
使用了标志-XX:+HeapDumpOnOutOfMemoryError，JVM会在遇到OutOfMemoryError时拍摄一个“堆转储快照”，并将其保存在一个文件中。  
-Xmx40m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=E:\Java\dump  
https://epy.iteye.com/blog/1914455  
-XX:+UseConcMarkSweepGC  
启用CMS算法  
-XX:+UseParNewGC  
设置年轻代为并发回收  
-XX:CMSInitiatingOccupancyFraction=70  
年老代使用了70%时回收内存   
-XX:-CMSConcurrentMTEnabled(-号代表否认)  
当该标志被启用时,并发的CMS阶段将以多线程执行(因此,多个GC线程会与所有的应用程序线程并行工作)  
该标志已经默认开启,如果顺序执行更好,这取决于所用的硬件,多线程执行可以通过-XX:-CMSConcurrentMTDisabled禁用  
-XX:+CMSIncrementalMode.  
在增量模式下,CMS收集器在并发阶段,不会独占整个周期,而会周期性的暂停,唤醒应用线程. 收集器把并发阶段工作h,划分为片段,安排在次级回收之间运行。
这对需要低延迟,运行在少量CPU服务器上的应用很有用。





### 3.regionservers  
>配置集群中运行RegionServer的节点列表,一行一个主机服务器


### 4.backup-master  
>备份主实例的每个节点,除非主设备不可用,否则备份主设备实例将处于空闲状态。  
>备份master主机名,例如: slave02  

## Hbase启动
启动zookeeper  
```shell-session
zkServer.sh start 
``` 
启动hdfs  
```shell-session
start-dfs.sh  
``` 

启动hbase  
```shell-session
start-hbase.sh  
``` 

## 遗留问题
### 1. 配置文件中,配置项的具体意义！  
### 2. Hbase-env.sh中的有关java配置的参数; 
### 3. Hbase如何性能测试;  
### 4. Hbase如何进行调优;  
[Hbase GC调优原理分析](http://www.zhangrenhua.com/2016/01/20/hadoop-HbaseGC%E8%B0%83%E4%BC%98%E5%8E%9F%E7%90%86%E5%88%86%E6%9E%90/)  
[Hbase 调优](http://www.cnblogs.com/ggjucheng/p/3381882.html)  
### 5. Hbase的基础原理;  


