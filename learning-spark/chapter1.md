#### chapter 1: Introduction to Apache Spark: A Unified Analytics Engine

- While *GFS* provided a fault-tolerant and *distributed filesystem* across many com
modity hardware servers in a cluster farm, *Bigtable* offered scalable storage of
structured data across GFS. *MR* introduced a new parallel programming paradigm, based on functional programming, for large-scale processing of data distributed over GFS and Bigtable.
-  The central thrust of the Spark project was to bring in ideas borrowed from Hadoop MapReduce, 
but to enhance the system: make it highly fault tolerant and embarrassingly
parallel, support in-memory storage for intermediate results between iterative and
interactive map and reduce computations, offer easy and composable APIs in multiple 
languages as a programming model, and support other workloads in a unified
manner. 
- **Apache Spark** is a *unified* engine designed for large-scale *distributed data processing*,
on premises in data centers or in the cloud
- Spark in terms of speed:
  - 1st. multithreading and parallel computation
  - 2nd. Spark builds its query computations as a *directed acyclic graph (DAG)*; its
  DAG scheduler and query optimizer construct an efficient computational graph that
  can usually be decomposed into tasks that are executed in *parallel* across workers on
  the cluster.
  - 3rd. its physical execution engine, *Tungsten*, uses whole-stage code
    generation to generate compact code for execution
- Spark achieves simplicity by providing a fundamental abstraction of a simple logical
data structure called a *Resilient Distributed Dataset (RDD)* upon which all other
higher-level structured data abstractions, such as *DataFrames* and *Datasets*, are constructed. 
By providing a set of *transformations* and *actions* as *operations*, Spark offers
a simple programming model that you can use to build big data applications in familiar languages.
- [what is DAG?](https://sparkbyexamples.com/spark/what-is-dag-in-spark/)
- Spark offers unified libraries with well-documented APIs that include the following modules 
as core components: *Spark SQL*, *Spark Structured Streaming*, *Spark MLlib*, and
*GraphX*, combining all the workloads running under one engine.
- Spark’s `DataFrameReaders` and `DataFrameWriters` can also be extended to read data from other sources, such as Apache Kafka,
Kinesis, Azure Storage, and Amazon S3, into its logical data abstraction, on which it can operate.
- [third party spark packages](https://spark.apache.org/third-party-projects.html)

-  a **unified** stack of components that addresses diverse workloads under a single distributed fast engine.

-  <img src=../images/spark-api.png>
  
-  `spark.ml` The DataFrame-based API for machine learning. This APIs allow you to extract or transform features, build pipelines (for training and evaluating), and persist models (for saving and reloading them) during deployment.

-  Necessary for big data developers to combine and react in real time to both static data
and streaming data from engines like Apache Kafka and other streaming sources, **the new model views a stream as a continually growing table**, with new rows of data
appended at the end. Developers can merely treat this as a structured table and issue
queries against it as they would a static table.

- Spark offers four distinct components as libraries for diverse
workloads: *Spark SQL, Spark MLlib, Spark Structured Streaming, and GraphX*. Each
of these components is separate from Spark’s core fault-tolerant engine, in that you
use APIs to write your Spark application and Spark converts this into a DAG that is
executed by the core engine.

- GraphX: As the name suggests, GraphX is a library for manipulating graphs (e.g., social network 
graphs, routes and connection points, or network topology graphs) and performing 
graph-parallel computations. It offers the standard graph algorithms for
analysis, connections, and traversals, contributed by users in the community: the
available algorithms include PageRank, Connected Components, and Triangle
Counting.

##### Apache Spark's Distributed Execution and Architecture

- At a high level in the Spark architecture, a Spark
application consists of a **driver** program that is responsible for orchestrating parallel
operations on the Spark cluster. The driver accesses the distributed components in
the cluster—the Spark **executors** and cluster **manager—through** a `SparkSession`.

- <img src=../images/spark-arch.png>

- **Driver**: As the part of the Spark application responsible for instantiating a `SparkSession`, the
Spark driver has multiple roles: it communicates with the cluster manager; it requests
resources (CPU, memory, etc.) from the cluster manager for Spark’s executors
(JVMs); and it transforms all the Spark operations into DAG computations, schedules them, and distributes their execution as tasks across the Spark executors. Once the
resources are allocated, it communicates directly with the executors.

- Through this one conduit (`SparkSession`), you can create JVM runtime parameters, define DataFrames 
and Datasets, read from data sources, access catalog metadata, and issue
Spark SQL queries. SparkSession provides a single unified entry point to all of
Spark's functionality, In a standalone Spark application, you can create a SparkSession using one of the
high-level APIs in the programming language of your choice. In the Spark shell
the SparkSession is created for you, and you can
access it via a global variable called *spark* or *sc*.

- The **cluster manager** is responsible for managing and allocating resources for the
cluster of nodes on which your Spark application runs.

- A Spark executor runs on each worker node in the cluster. The executors communi‐
cate with the driver program and are responsible for executing tasks on the workers.
In most deployments modes, only a single executor runs per node.

- <img src=../images/spark-deployment.png>

- **Partitioning** allows for efficient **parallelism**. A distributed scheme of breaking up data
into chunks or partitions allows Spark executors to process only data that is close to
them, minimizing network bandwidth. That is, each executor’s core is assigned its
own data partition to work on

- **shuffling**: To achieve this grouping, data from one partition might need to be moved to another partition, potentially residing on a different machine within the cluster. This movement and reorganization of data are collectively termed shuffling.

- [more on Spark API](https://www.databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html)

- Spark’s MLlib offers a common set
of machine learning algorithms to build model pipelines, using high-level estimators,
transformers, and data featurizers. 

- [Continuous Applications: Evolving Streaming in Apache Spark](https://www.databricks.com/blog/2016/07/28/continuous-applications-evolving-streaming-in-apache-spark-2-0.html)

- Data engineers use Spark because it provides a simple way to parallelize computations
and hides all the complexity of distribution and fault tolerance. This leaves them free
to focus on using high-level DataFrame-based APIs and domain-specific language
(DSL) queries to do ETL, reading and combining data from multiple sources.

- Spark computations are expressed as *operations*. These operations are then converted into low-level RDD-based bytecode as *tasks*,
which are distributed to Spark’s executors for execution.