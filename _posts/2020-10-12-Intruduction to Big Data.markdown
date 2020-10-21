---
title: "Introduction to Big Data"
published: true
---



# 1. Hadoop

http://www-01.ibm.com/software/data/infosphere/hadoop/

Apache Hadoop is an open source software project that enables distributed processing of large data sets across clusters of commodity servers. 

It is designed to scale up from a single server to thousands of machines, with very high degree of fault tolerance. 
Rather than relying on high-end hardware, the resiliency of these clusters comes from the software's ability to detect and handle failures at the application layer. 

## 1.1 Hadoop Architecture
### 1.1.1 Hadoop Core Projects

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

### 1.1.2 Hadoop Data Access Projects

Pig: A programming language designed to handle any type of data, helping users to focus more on analyzing large data sets and less on writing map programs and reduce programs. 

Hive: A Hadoop runtime component that allows those fluent with SQL to write Hive Query Language (HQL) statements, which are similar to SQL statements. These are broken down into MapReduce jobs and executed across the cluster.

Flume: A distributed, reliable and available service for efficiently collecting, aggregating and moving large amounts of log data. Its main goal is to deliver data from applications to the HDFS.

Hcatalog: A table and storage management service for Hadoop data that presents a table abstraction so the user does not need to know where or how the data is stored.

Avro: An Apache open source project that provides data serialization and data exchange services for Hadoop. 

Spark: An open-source cluster computing framework with in-memory analytics performance that is up to 100 times faster than MapReduce, depending on the application.

Sqoop: An ELT tool to support the transfer of data between Hadoop and structured data sources.  *ELT: extract, load, transform*

Hbase: A column-oriented non-relational (noSQL) database that runs on top of HDFS and is often used for sparse data sets. 

### 1.1.3 Hadoop Administration and Security Projects

Kerberos:
A network authentication protocol that works on the basis of “tickets” to allow nodes communicating over a non-secure network to prove their identity to one another in a secure manner.

Oozie:
A management application that simplifies workflow and coordination between MapReduce jobs. 

Zookeeper:
A centralized infrastructure and set of services that enable synchronization across a cluster. 

### 1.1.4 Hadoop Search Projects
Solr:
An enterprise search tool from the Apache Lucene project that offers powerful search capabilities, including hit highlighting, as well as indexing capabilities, reliability and scalability, a central configuration system, and failover and recovery.


## 1.2 Hadoop Cluster
A small Hadoop cluster includes a single master and multiple worker nodes. The master node consists of a JobTracker, TaskTracker, NameNode and DataNode. A slave or worker node acts as both a DataNode and TaskTracker, though it is possible to have data-only worker nodes and compute-only worker nodes. These are normally used only in nonstandard applications.

Hadoop requires Java Runtime Environment (JRE) 1.6 or higher. The standard startup and shutdown scripts require that Secure Shell (ssh) be set up between nodes in the cluster.

In a larger cluster, the HDFS is managed through a dedicated NameNode server to host the file system index, and a secondary NameNode that can generate snapshots of the namenode's memory structures, thus preventing file-system corruption and reducing loss of data. Similarly, a standalone JobTracker server can manage job scheduling. In clusters where the Hadoop MapReduce engine is deployed against an alternate file system, the NameNode, secondary NameNode, and DataNode architecture of HDFS are replaced by the file-system-specific equivalents.

## 1.3 HDFS
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

## 1.4 MapReduce
Hadoop uses its own RPC protocol. *(Remote Procedure Call Protocol).*
All communication begins in slave nodes
Prevents circular-wait deadlock.
Slaves periodically poll for “status” message
Classes must provide explicit serialization 

**Master** node runs JobTracker instance, which accepts Job requests from clients.
**TaskTracker** instances run on slave nodes.
TaskTracker forks separate Java process for task instances

![](/assets/bigdata2.png)

### 1.4.1 Data Distribution

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

### 1.4.2 MapReduce-Fault tolerance
<ol>
<li>Worker failure</li>
<li>Master failure</li>
</ol>

### 1.4.3 Task Granularity
The map phase has **M** pieces and the reduce phase has **R** pieces. M and R should be **much larger** than the number of worker machines. 

Having each worker perform many different tasks improves dynamic load balancing, and also speeds up recovery when a worker fails.

Larger the M and R, more the decisions the master must make. R is often constrained by users because the output of each reduce task ends up in a separate output file.
Typically,  (at Google), M = 200,000 and R = 5,000, using 2,000 worker machines.

### 1.4.4 Points need to be emphasized

<ul>
<li>No reduce can begin until map is complete</li>
<li>Master must communicate locations of intermediate files</li>
<li>Tasks scheduled based on location of data</li>
<li>If map worker fails any time before reduce finishes, task must be completely rerun</li>
<li>MapReduce library does most of the hard work for us!</li>


</ul>

### 1.4.5 Using Pig Script

Calculates the total miles flown for all flights flown in one year:


![](/assets/bigdata5.png)

```
Execute it: pig totalmiles.pig
See result: hdfs dfs –cat /user/root/totalmiles/part-r-00000
->775009272
```

## 1.5 Spark
Apache Spark is an open-source cluster computing framework originally developed in the AMPLab at UC Berkeley. In contrast to Hadoop's two-stage disk-based MapReduce paradigm, Spark's in-memory primitives provide performance up to 100 times faster for certain applications. By allowing user programs to load data into a cluster's memory and query it repeatedly, Spark is well suited to machine learning algorithms.

Spark requires a cluster manager and a distributed storage system. For cluster management, Spark supports standalone (native Spark cluster), Hadoop YARN, or Apache Mesos. 

For distributed storage, Spark can interface with a wide variety, including Hadoop Distributed File System (HDFS), Cassandra, OpenStack Swift, and Amazon S3. 

Spark also supports a pseudo-distributed local mode, usually used only for development or testing purposes, where distributed storage is not required and the local file system can be used instead; in this scenario, Spark is running on a single machine with one executor per CPU core.

*MapReduce has some inherent problems:*

*1st：user needs to write bottom level codes, low efficiency.*

*2nd: Map/Reduce cannot actually solve all types of problems.*

*Spark solves the above problems.*

### 1.5.1 Programming Model
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

## 1.6 Hive

Apache Hive is a data warehouse infrastructure built on top of Hadoop for providing data summarization, query, and analysis. While initially developed by Facebook, Apache Hive is now used and developed by other companies such as Netflix. Amazon maintains a software fork of Apache Hive that is included in Amazon Elastic MapReduce on Amazon Web Service<!--  -->s.

![](/assets/bigdata6.png)

## 1.7 Apache Tez

Tez is an extensible framework for building high performance batch and interactive data processing applications, coordinated by YARN in Apache Hadoop. Tez improves the MapReduce paradigm by dramatically improving its speed, while maintaining MapReduce’s ability to scale to petabytes of data. Important Hadoop ecosystem projects like Apache Hive and Apache Pig use Apache Tez, as do a growing number of third party data access applications developed for the broader Hadoop ecosystem.

Apache Tez provides a developer API and framework to write native YARN applications that bridge the spectrum of interactive and batch workloads. It allows those data access applications to work with petabytes of data over thousands nodes. The Apache Tez component library allows developers to create Hadoop applications that integrate natively with Apache Hadoop YARN and perform well within mixed workload clusters.

Since Tez is extensible and embeddable, it provides the fit-to-purpose freedom to express highly optimized data processing applications, giving them an advantage over end-user-facing engines such as MapReduce and Apache Spark. Tez also offers a customizable execution architecture that allows users to express complex computations as dataflow graphs, permitting dynamic performance optimizations based on real information about the data and the resources required to process it.

## 1.8 Apache Storm

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

# 2. Spark (in detail)

## 2.1 Spark Basis

### 2.1.1 Spark Goals

1. Acyclic data flow is a powerful abstraction, but is not efficient for applications that repeatedly reuse a working set of data:

  <ul>-Iterative algorithms (many in machine learning)

  -Interactive data mining tools (R, Excel, Python)</ul>

2. Spark makes working sets a first-class concept to efficiently support these apps

3. Provide distributed memory abstractions for clusters to support apps with working sets

4. Retain the attractive properties of MapReduce:

<ul>-Fault tolerance (for crashes & stragglers)

-Data locality

-Scalability</ul>

**Solution: augment data flow model with “resilient distributed datasets” (RDDs)**

**RDDs = first-class way to manipulate and persist intermediate datasets**

### 2.1.2 What is Spark

Fast and general engine for large scale data processing
Advanced DAG execution engine with support for

<ul> in-memory storage

 data locality

 (micro) batch & streaming support</ul>

Improves usability via

 <ul>Rich APIs in Scala, Java, Python

 Interactive shell</ul>

Runs Standalone, on YARN, on Mesos, and on Amazon EC2

## 2.2 Programming Model: RDD

### 2.2.1 RDD basic
An RDD is an immutable, partitioned, logical collection of records

Need not be materialized, but rather contains information to rebuild a dataset from stable storage

Partitioning can be based on a key in each record (using hash or range partitioning)

Built using bulk transformations on other RDDs
Can be cached for future reuse

Spark can create RDDs from any file stored in HDFS or other storage system supported by Hadoop, e.g, local file system, Amazon S3, HBase, etc.

### 2.2.2 RDD Operations

Two types of operations on RDDs:

<ul><li>transformations</li>
  <li>actions</li></ul>

A **transformation** is a **lazy** ***(not computed immediately, are only computed when an action requires it)*** operation on an RDD that yields another RDD

![](/assets/bigdata7.png)

![](/assets/bigdata8.png)

An **action** is an operation that triggers a computation, returns a value back to the Master, or writes to a stable storage system

![](/assets/bigdata9.png)

![](/assets/bigdata10.png)

### 2.2.3 RDD fault tolerance

RDDs maintain *lineage* information that can be used to reconstruct lost partitions

```
cachedMsgs = textFile(...).filter(_.contains(“error”))
                          .map(_.split(‘\t’)(2))
                          .cache()

```
```
CachedRDD --> MappedRDD    -->   FilteredRDD    --> 
            func: split(…)    func: contains(...)
--> HdfsRDD
path: hdfs://…
```

## 2.3 Example

### 2.3.1 Logistic Regression

```
val data = spark.textFile(...).map(readPoint).cache()

var w = Vector.random(D)

for (i <- 1 to ITERATIONS) {
  val gradient = data.map(p =>
    (1 / (1 + exp(-p.y*(w dot p.x))) - 1) * p.y * p.x
  ).reduce(_ + _)
  w -= gradient
}

println("Final w: " + w)
```

### 2.3.2 Word Counting

```
val lines = spark.textFile(“hdfs://...”)

val counts = lines.flatMap(_.split(“\\s”))
                  .reduceByKey(_ + _)

counts.save(“hdfs://...”)
```

## 2.4 Spark Implementation

![](/assets/bigdata11.png)

### 2.4.1 Spark: Parallelism

RDD partition number

<ul>• sc.textFile("input", minSplits = 10)

• sc.parallelize(1 to 10000, numSlices = 10)</ul>

Mapper side parallelism

<ul>• Usually inherited from parent RDD(s)</ul>

Reducer side parallelism

<ul>
• rdd.reduceByKey(_ + _, numPartitions = 10)

• rdd.reduceByKey(partitioner = p, _ + _)
</ul>

“Zoom in/out”

<ul>
• rdd.repartition(numPartitions: Int)

• rdd.coalesce(numPartitions: Int, shuffle: Boolean)
</ul>

![](/assets/bigdata12.png)

### 2.4.2 Spark: Streaming

Spark Streaming extends the core API to allow high-throughput, fault-tolerant stream processing of live data streams. **Data** can be ingested from many sources: Kafka, Flume, Twitter, ZeroMQ, TCP sockets… **Results** can be pushed out to filesystems, databases, live dashboards…
Spark’s MLlib algorithms and graph processing algorithms can be applied to data streams

![](/assets/bigdata13.png)

http://kafka.apache.org/index.html

http://flume.apache.org/

http://zeromq.org/

``` java
val ssc = new StreamingContext(sparkConf, Seconds(10))

# Create a StreamingContext by providing the configuration and batch duration
```

![](/assets/bigdata14.png)

<ol><li>Discretized Stream</li>

<li>Windowed Computations
</li></ol>

![](/assets/bigdata15.png)
![](/assets/bigdata16.png)

### 2.4.3 Spark: SQL

Spark SQL blurs the lines between RDDs and relational tables

``` java
val conf = new SparkConf().setAppName("SparkSQL")
val sc = new SparkContext(conf)
val peopleFile = args(0)

val sqlContext = new org.apache.spark.sql.SQLContext(sc)
import sqlContext._

// Define the schema using a case class.
case class Person(name: String, age: Int)

// Create an RDD of Person objects and register it as a table.
val people = sc.textFile(peopleFile).map(_.split(",")).map(p => Person(p(0), p(1).trim.toInt))

people.registerAsTable("people")

// SQL statements can be run by using the sql methods provided by sqlContext.
val teenagers = sql("SELECT name FROM people WHERE age >= 13 AND age <= 19")

// The results of SQL queries are SchemaRDDs and support all the normal RDD operations.
// The columns of a row in the result can be accessed by ordinal.
teenagers.map(t => "Name: " + t(0)).collect().foreach(println)

```

### 2.4.4 Spark: GraphX
GraphX is the Spark API for graphs and graph-parallel computation

API’s to join and traverse graphs

Optimally partitions and indexes vertices & edges (represented as RDD’s)

Supports PageRank, connected components, triangle counting, …

``` java
case class Peep(name: String, age: Int)
val vertexArray = Array(
    (1L, Peep("Kim", 23)), (2L, Peep("Pat", 31)),
    (3L, Peep("Chris", 52)), (4L, Peep("Kelly", 39)),
    (5L, Peep("Leslie", 45)))
val edgeArray = Array(
    Edge(2L, 1L, 7), Edge(2L, 4L, 2),
    Edge(3L, 2L, 4), Edge(3L, 5L, 3),
    Edge(4L, 1L, 1), Edge(5L, 3L, 9))

val conf = new SparkConf().setAppName("SparkGraphx")
val sc = new SparkContext(conf)
val vertexRDD: RDD[(Long, Peep)] = sc.parallelize(vertexArray)
val edgeRDD: RDD[Edge[Int]] = sc.parallelize(edgeArray)
val g: Graph[Peep, Int] = Graph(vertexRDD, edgeRDD)

val results = g.triplets.filter(t => t.attr > 7)
for (triplet <- results.collect) {
    println(s"${triplet.srcAttr.name} loves ${triplet.dstAttr.name}")
}

```

### 2.4.5 Spark: MLlib

MLlib is Spark’s scalable machine learning library consisting of common learning algorithms and utilities.

### 2.4.6 Spark: Persistence

One of the most important capabilities in Spark is caching a dataset in-memory across operations

Spark can persist (or cache) a dataset in memory across operations

Each node stores in memory any slices of it that it computes and reuses them in other actions on that dataset
 – often making future actions more than 10x faster

The cache is fault-tolerant: if any partition of an RDD is lost, it will automatically be recomputed using the transformations that originally created it

![](/assets/bigdata17.png)

**Persistence Level:**

![](/assets/bigdata18.png)

### 2.4.7 SparkR: Interactive R at scale

SparkR is an R package that provides a light-weight frontend to use Apache Spark from R. SparkR exposes the Spark API through the RDD class and allows users to interactively run jobs from the R shell on a cluster.

# 3. Mahout

Apache Mahout is a project of the Apache Software Foundation to produce free implementations of distributed or otherwise scalable machine learning algorithms focused primarily in the areas of collaborative filtering, clustering and classification. Many of the implementations use the Apache Hadoop platform. Mahout also provides Java libraries for common maths operations (focused on linear algebra and statistics) and primitive Java collections. 

## 3.1 Recommendation using Mahout

Mahout implements a **Collaborative Filtering framework** 

<ul>
<li>User-based: Recommend items by finding similar users. This is often harder to scale because of the dynamic nature of users. 
</li><li>Item-based: Calculate similarity between items and make recommendations. Items usually don't change much, so this often can be computed offline. 
</li><li>Slope-One: A very fast and simple item-based recommendation approach applicable when users have given ratings (and not just Boolean preferences). 
</li></ul>

![](/assets/bigdata19.png)

### 3.1.1 Components

#### 3.1.1.1 Data Model

A DataModel is an interface to draw information about user preferences. 

<ul>
<li>Database: <b>JDBCDataModel</b> (JDBC Driver, Standard database structure ) 
</li><li>External Files: <b>FileDataModel</b> (CSV)
</li><li>Generic (preferences directly feed through Java code): <b>GenericDataModel</b> (Feed through Java calls) </li>
</ul>

1.Basic object: **Preference**
<ul>
–Preference is a triple (user,item,score) 

–Stored in UserPreferenceArray 
</ul>

![](/assets/bigdata20.png)

2.Implementation of data model

1. GenericUserPreferenceArray: stores numerical preference, as well 

2. BooleanUserPreferenceArray: skips numerical preference values

#### 3.1.1.2 UserSimilarity

UserSimilarity defines a notion of similarity between two Users. 

<ul><li>
Pearson Correlation 
</li><li>
Spearman Correlation 
</li><li>
Euclidean Distance 
</li><li>
Tanimoto Coefficient 
</li><li>
LogLikelihood Similarity 
</li>
</ul>

#### 3.1.1.3 UserNeighborhood

<ul>
<b>Nearest N users</b> 

•The first N users with the highest similarity are labeled as ‘neighbors’ 

<b>Thresholds</b>

•Users whose similarity is above a threshold are labeled as ‘neighbors’ 
</ul>

#### 3.1.1.4 Recommender

Given a *DataModel*, a definition of *similarity* between users (items) and a definition of *neighborhood*, a *recommender* produces as output an estimation of relevance for each unseen item.
<ul>
-User-based CF 

–Item-based CF 

–SVD-based CF 

–SlopeOne CF 
</ul>

![](/assets/bigdata21.png)

### 3.1.2 Evaluation

**Prediction-based measures** 

•Mean Average Error 

•RMSE (Root Mean Square Error) 

```
–Class: AverageAbsoluteDifferenceEvaluator 
–Method: evaluate() 

–Parameters: 
•Recommender implementation 
•DataModel implementation 
•TrainingSet size (e.g. 70%) 
•% of the data to use in the evaluation (smaller % for fast prototyping) 
```

**IR-based measures**

•Precision 

•Recall 

•F1-measure 

•F1@n 

•NDCG (ranking measure) 

```
–Class: GenericRecommenderIRStatsEvaluator 
–Method: evaluate() 

–Parameters: 
•Recommender implementation 
•DataModel implementation 
•Relevance Threshold (mean+standard deviation) 
•% of the data to use in the evaluation (smaller % for fast prototyping) 
```

## 3.2 Recommendation Algorithms

### 3.2.1 User-based Recommendation Algorithms
![](/assets/bigdata22.png)

### 3.2.2 Item-based recommendation
![](/assets/bigdata23.png)

### 3.2.3 Slope-One Algorithm

![](/assets/bigdata24.png)

# 4. Block Chain

## 4.1 Motivation
Problem : Slow Payment Transactions (because of each institution uses different ledgers)

Almost completely manual with a single “transaction” taking as long as 50 days!

*“The trade happens in seconds, but it takes days to complete the transaction.”*

Solution:  a *digital* permissioned, replicated, ***shared ledger*** …enabled by open *Blockchain* technology

## 4.2 Concept

Blockchain is an (1) electronic, (2) distributed, (3) replicated, (4) decentralized Ledger

**Ledger** is THE system of record for a business
records asset transfer between participants.

Business will have multiple ledgers for multiple business networks in which they participate… hence may be “peers” in multiple blockchains…..

The **“Block”** is an executed & verified transaction

![](/assets/bigdata26.png)

The **“Chain”** is the Ledger 
(book of record for all transactions)

![](/assets/bigdata27.png)

![](/assets/bigdata28.png)

A **shared ledger technology** allowing any participant in the business network to see THE system of record (ledger)

**Smart Contracts**: Business rules implied by the contract, codified in the Blockchain and executed with the transaction which are verifiable and signed.

### 4.2.1 Key Concepts


#### 4.2.1.1 Distributed shared ledgers

Group of replicated logs/databases (nodes)

 Transactions distributed in blocks

 All nodes hold all transactions


 Parties identified with public key (= anonymised)
 Accessibility of transactions depending on blockchain 
 implementation

 Resilient for failure of one or more nodes

 Group of nodes operate tamper proof


#### 4.2.1.2 Consensus

Consensus = Majority of nodes agree on validity of transactions

 Includes validation on double-spending

 Permissionless (public) vs. permissioned (private) blockchain setup

 Proof-of-work / proof-of-stake the proof validity of node
	(only applicable for permissionless network)

#### 4.2.1.3 Cryptography 

![](/assets/bigdata25.png)

#### 4.2.1.4 Smart Contracts

Business logic that can be assigned to a transaction on the blockchain

 Acts as a ‘notary’ of blockchain transactions

 Holds conditions under which specific actions can/must be performed

 Facilitates escrow services

 Can’t be modified without predefined permissions

## 4.3 Use Cases

### 4.3.1 Bitcoin

### 4.3.2 Ethereum

### 4.3.3 Smart Business Contract

![](/assets/bigdata29.png)

## 4.4 Demo 

https://www.youtube.com/watch?v=EqZr4LPQIWk

**Blockchain on IBM Systems**

![](/assets/bigdata30.png)
