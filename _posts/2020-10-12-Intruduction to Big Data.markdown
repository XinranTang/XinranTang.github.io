---
title: "Introduction to Big Data"
published: true
---


# Introduction to Big Data
## Hadoop

http://www-01.ibm.com/software/data/infosphere/hadoop/

Apache Hadoop is an open source software project that enables distributed processing of large data sets across clusters of commodity servers. 

It is designed to scale up from a single server to thousands of machines, with very high degree of fault tolerance. 
Rather than relying on high-end hardware, the resiliency of these clusters comes from the software's ability to detect and handle failures at the application layer. 

### Hadoop Architecture
#### Hadoop Core Projects
1. HDFS
2. YARN (Yet Another Source Negotiator): A framework for job scheduling and cluster resource management
3. MapReduce
4. Hadoop common: 
   1. provides filesystem and OS level abstractions
   2. contains the necessary Java ARchive (JAR) files and scripts needed to start Hadoop 
   3. provides source code, documentation, and a contribution section that includes projects from the Hadoop Community
   4. support the other Hadoop modules

#### Hadoop Data Access Projects

Pig: A programming language designed to handle any type of data, helping users to focus more on analyzing large data sets and less on writing map programs and reduce programs. 

Hive: A Hadoop runtime component that allows those fluent with SQL to write Hive Query Language (HQL) statements, which are similar to SQL statements. These are broken down into MapReduce jobs and executed across the cluster.

Flume: A distributed, reliable and available service for efficiently collecting, aggregating and moving large amounts of log data. Its main goal is to deliver data from applications to the HDFS.

Hcatalog: A table and storage management service for Hadoop data that presents a table abstraction so the user does not need to know where or how the data is stored.

Avro: An Apache open source project that provides data serialization and data exchange services for Hadoop. 

Spark: An open-source cluster computing framework with in-memory analytics performance that is up to 100 times faster than MapReduce, depending on the application.

Sqoop: An ELT tool to support the transfer of data between Hadoop and structured data sources.  *ELT: extract, load, transform*

Hbase: A column-oriented non-relational (noSQL) database that runs on top of HDFS and is often used for sparse data sets. 

#### Hadoop Administration and Security Projects

Kerberos:
A network authentication protocol that works on the basis of “tickets” to allow nodes communicating over a non-secure network to prove their identity to one another in a secure manner.

Oozie:
A management application that simplifies workflow and coordination between MapReduce jobs. 

Zookeeper:
A centralized infrastructure and set of services that enable synchronization across a cluster. 

#### Hadoop Search Projects
Solr:
An enterprise search tool from the Apache Lucene project that offers powerful search capabilities, including hit highlighting, as well as indexing capabilities, reliability and scalability, a central configuration system, and failover and recovery.


### Hadoop Cluster
A small Hadoop cluster includes a single master and multiple worker nodes. The master node consists of a JobTracker, TaskTracker, NameNode and DataNode. A slave or worker node acts as both a DataNode and TaskTracker, though it is possible to have data-only worker nodes and compute-only worker nodes. These are normally used only in nonstandard applications.

Hadoop requires Java Runtime Environment (JRE) 1.6 or higher. The standard startup and shutdown scripts require that Secure Shell (ssh) be set up between nodes in the cluster.

In a larger cluster, the HDFS is managed through a dedicated NameNode server to host the file system index, and a secondary NameNode that can generate snapshots of the namenode's memory structures, thus preventing file-system corruption and reducing loss of data. Similarly, a standalone JobTracker server can manage job scheduling. In clusters where the Hadoop MapReduce engine is deployed against an alternate file system, the NameNode, secondary NameNode, and DataNode architecture of HDFS are replaced by the file-system-specific equivalents.

### HDFS
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

