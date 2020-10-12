---
title: "Introduction to Big Data"
published: true
---


# Introduction to Big Data
## 1. Hadoop

http://www-01.ibm.com/software/data/infosphere/hadoop/

Apache Hadoop is an open source software project that enables distributed processing of large data sets across clusters of commodity servers. 

It is designed to scale up from a single server to thousands of machines, with very high degree of fault tolerance. 
Rather than relying on high-end hardware, the resiliency of these clusters comes from the software's ability to detect and handle failures at the application layer. 

### 1.1 Hadoop Architecture
#### 1.1.1 Hadoop Core Projects

<ol>
<li> HDFS</li>
<li> YARN (Yet Another Source Negotiator): A framework for job scheduling and cluster resource management</li>
<li> MapReduce</li>
<li> Hadoop common: 
   <ol><li> provides filesystem and OS level abstractions</li>
  <li> contains the necessary Java ARchive (JAR) files and scripts needed to start Hadoop </li>
   <li> provides source code, documentation, and a contribution section that includes projects from the Hadoop Community</li>
   <li>support the other Hadoop modules</li></ol></li>
</ol>

#### 1.1.2 Hadoop Data Access Projects

Pig: A programming language designed to handle any type of data, helping users to focus more on analyzing large data sets and less on writing map programs and reduce programs. 

Hive: A Hadoop runtime component that allows those fluent with SQL to write Hive Query Language (HQL) statements, which are similar to SQL statements. These are broken down into MapReduce jobs and executed across the cluster.

Flume: A distributed, reliable and available service for efficiently collecting, aggregating and moving large amounts of log data. Its main goal is to deliver data from applications to the HDFS.

Hcatalog: A table and storage management service for Hadoop data that presents a table abstraction so the user does not need to know where or how the data is stored.

Avro: An Apache open source project that provides data serialization and data exchange services for Hadoop. 

Spark: An open-source cluster computing framework with in-memory analytics performance that is up to 100 times faster than MapReduce, depending on the application.

Sqoop: An ELT tool to support the transfer of data between Hadoop and structured data sources.  *ELT: extract, load, transform*

Hbase: A column-oriented non-relational (noSQL) database that runs on top of HDFS and is often used for sparse data sets. 

#### 1.1.3 Hadoop Administration and Security Projects

Kerberos:
A network authentication protocol that works on the basis of “tickets” to allow nodes communicating over a non-secure network to prove their identity to one another in a secure manner.

Oozie:
A management application that simplifies workflow and coordination between MapReduce jobs. 

Zookeeper:
A centralized infrastructure and set of services that enable synchronization across a cluster. 

#### 1.1.4 Hadoop Search Projects
Solr:
An enterprise search tool from the Apache Lucene project that offers powerful search capabilities, including hit highlighting, as well as indexing capabilities, reliability and scalability, a central configuration system, and failover and recovery.


### 1.2 Hadoop Cluster
A small Hadoop cluster includes a single master and multiple worker nodes. The master node consists of a JobTracker, TaskTracker, NameNode and DataNode. A slave or worker node acts as both a DataNode and TaskTracker, though it is possible to have data-only worker nodes and compute-only worker nodes. These are normally used only in nonstandard applications.

Hadoop requires Java Runtime Environment (JRE) 1.6 or higher. The standard startup and shutdown scripts require that Secure Shell (ssh) be set up between nodes in the cluster.

In a larger cluster, the HDFS is managed through a dedicated NameNode server to host the file system index, and a secondary NameNode that can generate snapshots of the namenode's memory structures, thus preventing file-system corruption and reducing loss of data. Similarly, a standalone JobTracker server can manage job scheduling. In clusters where the Hadoop MapReduce engine is deployed against an alternate file system, the NameNode, secondary NameNode, and DataNode architecture of HDFS are replaced by the file-system-specific equivalents.

### 1.3 HDFS
![](/assets/bigdata1.png)
Hadoop is designed to work best with a modest number of extremely large files. *Average file sizes larger than 500MB.*
*Write Once, Read Often model.*

Content of individual files cannot be modified, other than appending new data at the end of the file.

**What we can do:**
<ul>
  <li>Create a new file</li><li>
Append content to the end of a file</li><li>
Delete a file</li><li>
Rename a file</li><li>
Modify file attributes like owner</li>
</ul>

File is divided into blocks (default: 64MB) and duplicated in multiple datanodes (default: 3)
Dividing into blocks is normal for a file system. E.g., the default block size in Linux is 4KB. The difference of HDFS is the scale.
Single namenode stores metadata (file names, block locations, etc.)
Every data block stored in HDFS has its own metadata and needs to be tracked by a central server.
When HDFS stores the replicas of the original blocks across the Hadoop cluster, it tries to ensure that the block replicas are stored in different failure points.

**Useful Commands**
<ul>
<li> hadoop fsck / -files –blocks</li>
<li> For HDFS, the schema name is hdfs, and for the local file system, the schema name is file. A file or director in HDFS can be specified in a fully qualified way, such as:
hdfs://namenodehost/parent/child or hdfs://namenodehost
</li>
<li> hadoop hdfs –file_cmd</li>
<li> hadoop hdfs dfs –mkdir /user/directory_name
</li>
</ul>

### 1.4 MapReduce
Hadoop uses its own RPC protocol. *(Remote Procedure Call Protocol).*
All communication begins in slave nodes
Prevents circular-wait deadlock.
Slaves periodically poll for “status” message
Classes must provide explicit serialization 

**Master** node runs JobTracker instance, which accepts Job requests from clients.
**TaskTracker** instances run on slave nodes.
TaskTracker forks separate Java process for task instances

![](/assets/bigdata2.png)

#### 1.4.1 Data Distribution

<ul>
<li>Implicit in design of MapReduce!
All mappers are equivalent; so map whatever data is local to a particular node in HDFS.
</li><li>
If lots of data does happen to pile up on the same node, nearby nodes will map instead.
Data transfer is handled implicitly by HDFS
</li>
<li>

**Map**:
Accepts input key/value pair
Emits intermediate key/value pair

```
square x = x * x
map square [1,2,3,4,5]
returns [1,4,9,16,25]

```

</li>
<li>

**Reduce** :
Accepts intermediate key/value pair.
Emits output key/value pair

```
sum = (each elem in arr, total +=)
reduce [1,2,3,4,5]
returns 15 (the sum of the elements)‏

```

</li>
<li>

**Partition** :
By default : hash(key) mod R.
Well balanced

</li>
</ul>

![](/assets/bigdata3.png)
![](/assets/bigdata4.png)

#### 1.4.2 MapReduce-Fault tolerance
<ol>
<li>Worker failure</li>
<li>Master failure</li>
</ol>

#### 1.4.3 Task Granularity
The map phase has **M** pieces and the reduce phase has **R** pieces. M and R should be **much larger** than the number of worker machines. 

Having each worker perform many different tasks improves dynamic load balancing, and also speeds up recovery when a worker fails.

Larger the M and R, more the decisions the master must make. R is often constrained by users because the output of each reduce task ends up in a separate output file.
Typically,  (at Google), M = 200,000 and R = 5,000, using 2,000 worker machines.

#### 1.4.4 Points need to be emphasized

<ul>
<li>No reduce can begin until map is complete</li>
<li>Master must communicate locations of intermediate files</li>
<li>Tasks scheduled based on location of data</li>
<li>If map worker fails any time before reduce finishes, task must be completely rerun</li>
<li>MapReduce library does most of the hard work for us!</li>


</ul>

#### 1.4.5 Using Pig Script

Calculates the total miles flown for all flights flown in one year:


![](/assets/bigdata5.png)

```
Execute it: pig totalmiles.pig
See result: hdfs dfs –cat /user/root/totalmiles/part-r-00000
->775009272
```

### 1.5 Spark
Apache Spark is an open-source cluster computing framework originally developed in the AMPLab at UC Berkeley. In contrast to Hadoop's two-stage disk-based MapReduce paradigm, Spark's in-memory primitives provide performance up to 100 times faster for certain applications. By allowing user programs to load data into a cluster's memory and query it repeatedly, Spark is well suited to machine learning algorithms.

Spark requires a cluster manager and a distributed storage system. For cluster management, Spark supports standalone (native Spark cluster), Hadoop YARN, or Apache Mesos. 

For distributed storage, Spark can interface with a wide variety, including Hadoop Distributed File System (HDFS), Cassandra, OpenStack Swift, and Amazon S3. 

Spark also supports a pseudo-distributed local mode, usually used only for development or testing purposes, where distributed storage is not required and the local file system can be used instead; in this scenario, Spark is running on a single machine with one executor per CPU core.

*MapReduce has some inherent problems:*

*1st：user needs to write bottom level codes, low efficiency.*

*2nd: Map/Reduce cannot actually solve all types of problems.*

*Spark solves the above problems.*

#### 1.5.1 Programming Model
<ol>
<li>

**Resilient distributed datasets (RDDs)**

Immutable collections partitioned across cluster that can be rebuilt if a partition is lost

Created by transforming data in stable storage using data flow operators (map, filter, group-by, …)

Can be cached across parallel operations

Two types of operations on RDDs: 

**transformations and actions**


</li>

<li>
Parallel operations on RDDs

Reduce, collect, count, save, …

</li>
</ol>

### 1.6 Hive

Apache Hive is a data warehouse infrastructure built on top of Hadoop for providing data summarization, query, and analysis. While initially developed by Facebook, Apache Hive is now used and developed by other companies such as Netflix. Amazon maintains a software fork of Apache Hive that is included in Amazon Elastic MapReduce on Amazon Web Services.

![](/assets/bigdata6.png)

### 1.7 Apache Tez

Tez is an extensible framework for building high performance batch and interactive data processing applications, coordinated by YARN in Apache Hadoop. Tez improves the MapReduce paradigm by dramatically improving its speed, while maintaining MapReduce’s ability to scale to petabytes of data. Important Hadoop ecosystem projects like Apache Hive and Apache Pig use Apache Tez, as do a growing number of third party data access applications developed for the broader Hadoop ecosystem.

Apache Tez provides a developer API and framework to write native YARN applications that bridge the spectrum of interactive and batch workloads. It allows those data access applications to work with petabytes of data over thousands nodes. The Apache Tez component library allows developers to create Hadoop applications that integrate natively with Apache Hadoop YARN and perform well within mixed workload clusters.

Since Tez is extensible and embeddable, it provides the fit-to-purpose freedom to express highly optimized data processing applications, giving them an advantage over end-user-facing engines such as MapReduce and Apache Spark. Tez also offers a customizable execution architecture that allows users to express complex computations as dataflow graphs, permitting dynamic performance optimizations based on real information about the data and the resources required to process it.

### 1.8 Apache Storm

Stream Processing
-- On Hadoop, you run MapReduce jobs; On Storm, you run Topologies.

A storm cluster has three sets of nodes:
Nimbus node (master node, similar to the Hadoop JobTracker): 

Uploads computations for execution

Distributes code across the cluster

Launches workers across the cluster

Monitors computation and reallocates workers as needed

ZooKeeper nodes – coordinates the Storm cluster

Supervisor nodes – communicates with Nimbus through Zookeeper, starts and stops workers according to signals from Nimbus
