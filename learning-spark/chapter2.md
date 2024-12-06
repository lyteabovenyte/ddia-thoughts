#### chapter 2. Downloading Apache Spark and Getting Started

- be familiar with some of the key concepts of a Spark application and how the code is transformed and executed as tasks across the Spark executors.
  - *Application*: A user program built on Spark using its APIs. It consists of a driver program and
    executors on the cluster.
  - *SparkSession*: An object that provides a point of entry to interact with underlying Spark func‐
    tionality and allows programming Spark with its APIs. In an interactive Spark
    shell, the Spark driver instantiates a SparkSession for you, while in a Spark
    application, you create a SparkSession object yourself.
  - *Job*: A parallel computation consisting of multiple tasks that gets spawned in response
    to a Spark action (e.g., `save()`, `collect()`)
  - *Stage*: Each job gets divided into smaller sets of tasks called stages that depend on each
    other.
  - *Task*: A single unit of work or execution that will be sent to a Spark executor.

- Spark components communicate through the Spark *driver* in Spark’s dis‐
tributed architecture

##### Spark execution plan.

  - **Spark Jobs**: During interactive sessions with Spark shells, the driver converts your Spark application 
  into one or more Spark jobs. It then transforms each job into a
  DAG. This, in essence, is Spark’s execution plan, where each node within a DAG
  could be a single or multiple Spark *stages*.

  - **Spark Stages**: As part of the DAG nodes, stages are created based on what operations can be per‐
    formed serially or in parallel. Not all Spark operations can happen in a
    single stage, so they may be divided into multiple stages. Often stages are delineated
    on the operator’s computation boundaries, where they dictate data transfer among
    Spark executors.

    - **Spark Tasks**: Each stage is comprised of Spark tasks (a unit of execution), which are then federated
    across each Spark executor; each task maps to a single core and works on a single par‐
    tition of data. As such, an executor with 16 cores can have 16 or more
    tasks working on 16 or more partitions in parallel, making the execution of Spark’s
    tasks exceedingly parallel!


- **Lazy transformations and eager actions**: Spark operations on distributed data can be classified into two types: **transformations**
and **actions**. Transformations, as the name suggests, transform a Spark DataFrame
into a new DataFrame without altering the original data, giving it the property of
immutability. Put another way, an operation such as select() or filter() will not
change the original DataFrame; instead, it will return the transformed results of the operation 
as a new DataFrame. All transformations are evaluated lazily. That is, their results are not computed immediately, 
but they are recorded or remembered as a **lineage**. A recorded lineage allows
Spark, at a later time in its execution plan, to rearrange certain transformations, *coalesce* 
them, or optimize transformations into stages for more efficient execution. Lazy
evaluation is Spark’s strategy for delaying execution until an action is invoked or data
is “touched” (read from or written to disk). An **action** triggers the lazy evaluation of all the recorded transformations. 

- **Lineage** refers to the data lifecycle, detailing the series of steps that data has undergone from its source to its current form. In Spark, lineage is built using the RDD (Resilient Distributed Dataset) abstraction, *which keeps track of all transformations applied to it*.

- While lazy evaluation allows Spark to optimize your queries by peeking into your
chained transformations, lineage and data immutability provide fault tolerance.

- The actions and transformations contribute to a *Spark query plan*

- Transformations can be classified as having either *narrow* dependencies or *wide*
dependencies. Any transformation where a single output partition can be computed
from a single input partition is a narrow transformation. For example, in the previous
code snippet, filter() and contains() represent narrow transformations because
they can operate on a single partition and produce the resulting output partition
without any exchange of data. However, `groupBy()`or `orderBy()` instruct Spark to perform wide transformations,
where data from other partitions is read in, combined, and written to disk. Since each
partition will have its own count of the word that contains the “Spark” word in its row
of data, a count (groupBy()) will force a shuffle of data from each of the executor’s
partitions across the cluster. In this transformation, orderBy() requires output from
other partitions to compute the final aggregation.

- Spark job is split into multiple stages at the points where data shuffling is needed, 
and each stage is split into tasks that run the same code on different data partitions.
[more on the concepts of Jobs, Stages, Tasks](https://medium.com/@diehardankush/what-are-job-stage-and-task-in-apache-spark-2fc0d326c15f)
