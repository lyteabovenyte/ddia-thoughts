&nbsp;
<span style="color: #f2cf4a; font-family: Monospace; font-size: 3em;">Database internals</span>

<span style="color: #f2cf4a; font-family: monospace; font-size: 2em;">Chapter 1</span>

- Databases are modular systems and consist of multiple parts: a transport layer accepting requests, a query processor determining the most efficient way to run queries, an execution engine carrying out the operations, and a storage engine

- The storage engine (or database engine) is a software component of a database management system responsible for storing, retrieving, and managing data in memory and on disk, designed to capture a persistent, long-term memory of each node. While databases can respond to complex queries, storage engines look at the data more granularly and offer a simple data manipulation API, allowing users to create, update, delete, and retrieve records. One way to look at this is that database management systems are applications built on top of storage engines, offering a schema, a query language, indexing, transactions, and many other useful features.

##### questions to choose the best database for individual need:
- Does the database support the required queries?
- Is this database able to handle the amount of data we’re planning to store?
- How many read and write operations can a single node handle? How many nodes should the system have?
- How do we expand the cluster given the expected growth rate? What is the maintenance process?

###### how to answer them: 
To compare databases, it’s helpful to understand the use case in great detail and define the current and anticipated variables, such as:
- Schema and record sizes
- Number of clients
- Types of queries and access patterns
- Rates of the read and write queries
- Expected changes in any of these variables

- If the tests provided by common stress tools show positive results, it may be helpful to familiarize yourself with the database code. Looking at the code, it is often useful to first understand the parts of the database, how to find the code for different components, and then navigate through those. **Having even a rough idea about the database codebase helps you better understand the log records it produces, its configuration parameters, and helps you find issues in the application that uses it**

- *Benchmarks* can be useful to define and test details of the service-level agreement(SLA) understanding system requirements, capacity planning, and more.

- Database management systems can serve different purposes: some are used primarily for temporary hot data, some serve as a long-lived cold storage, some allow complex analytical queries, some only allow accessing values by the key, some are optimized to store time-series data, and some store large blobs efficiently. 


some sources group DBMSs into three major categories:
  - 1) Online transaction processing (OLTP) databases: 
        - These handle a large number of user-facing requests and transactions. Queries are often predefined and short-lived.
  - 2) Online analytical processing (OLAP) databases:
        - These handle complex aggregations. OLAP databases are often used for analytics and data warehousing, and are capable of handling complex, long-running ad hoc queries.
  - 3) Hybrid transactional and analytical processing (HTAP):
        - These databases combine properties of both OLTP and OLAP stores.



- There are many other terms and classifications: key-value stores, relational databases, document-oriented stores, and graph databases.

- Database management systems use a client/server model, where database system instances (nodes) take the role of servers, and application instances take the role of clients.

- Client requests arrive through the *transport subsystem*. Requests come in the form of queries, most often expressed in some query language. The transport subsystem is also responsible for communication with other nodes in the database cluster.

&nbsp;

###### Architecture of database management systems:  

<img src=../images/figure1-1.png alt="alt text" width="400" height="500"/>    
         
&nbsp;

- Upon receipt, the transport subsystem hands the query over to a query processor, which parses, interprets, and validates it. Later, access control checks are performed, as they can be done fully only after the query is interpreted.
The parsed query is passed to the query optimizer, which first eliminates impossible and redundant parts of the query, and then attempts to find the most efficient way to execute it based on internal statistics (index cardinality, approximate intersection size, etc.) and data placement (which nodes in the cluster hold the data and the costs associated with its transfer). The optimizer handles both relational operations required for query resolution, usually presented as a dependency tree, and optimizations, such as index ordering, cardinality estimation, and choosing access methods.

- The query is usually presented in the form of an execution plan (or query plan): a sequence of operations that have to be carried out for its results to be considered complete

- The execution plan is handled by the *execution engine*, which collects the results of the execution of local and remote operations. Remote execution can involve writing and reading data to and from other nodes in the cluster, and replication.

- *Local queries* (coming directly from clients or from other nodes) are executed by the storage engine

- Transaction manager: This manager schedules transactions and ensures they cannot leave the database in a logically inconsistent state.

- Lock manager: This manager locks on the database objects for the running transactions, ensuring that concurrent operations do not violate physical data integrity.

- Access methods (storage structures): These manage access and organizing data on disk. Access methods include heap files and storage structures such as B-Trees or LSM Trees.

- Buffer manager: This manager caches data pages in memory

- Recovery manager: this manager maintains the oparation log and restoring the system state in the case of failure

- Together, transaction and lock managers are responsible for concurrency control, they guarantee logical and physical data integrity while ensuring that concurrent operations are executed as efficiently as possible.

- Database systems store data in memory and on disk. In-memory database management systems (sometimes called main memory DBMS) store data primarily in memory and use the disk for recovery and logging. Disk-based DBMS hold most of the data on disk and use memory for caching disk contents

- Main memory database systems are different from their disk-based counterparts not only in terms of a primary storage medium, but also in which data structures, organization, and optimization techniques they use.

- Databases using memory as a primary data store do this mainly because of performance, comparatively low access costs, and access granularity. Programming for main memory is also significantly simpler than doing so for the disk. Operating systems abstract memory management and allow us to think in terms of allocating and freeing arbitrarily sized memory chunks. On disk, we have to manage data references, serialization formats, freed memory, and fragmentation manually.

- Before the operation can be considered complete, its results have to be written to a *sequential log* file. To avoid replaying complete log contents during startup or after a crash, in-memory stores maintain a **backup copy**. The backup copy is maintained as a sorted disk-based structure, and modifications to this structure are often *asynchronous* (decoupled from client requests) and applied in batches to reduce the number of I/O operations. During recovery, database contents can be restored from the backup and logs.

- Log records are usually applied to backup in batches. After the batch of log records is processed, backup holds a database snapshot for a specific point in time, and log contents up to this point can be discarded. this process is called *checkpointing*

- One of the ways to classify databases is by how the data is stored on disk: row or column-wise. Tables can be partitioned either *horizontally* (storing values belonging to the same row together), or *vertically* (storing values belonging to the same column together).

- **Column-oriented stores** are a good fit for analytical workloads that compute aggregates, such as finding trends, computing average values, etc. Processing complex aggregates can be used in cases when logical records have multiple fields, but some of them have different importance and are often consumed together.

- To reconstruct data tuples, which might be useful for joins, filtering, and multirow aggregates, we need to preserve some metadata on the column level to identify which data points from other columns it is associated with. If you do this explicitly, each value will have to hold a key, which introduces duplication and increases the amount of stored data. Some column stores use implicit identifiers (*virtual IDs*) instead and use the position of the value (in other words, its offset) to map it back to the related values

- Reading multiple values for the same column in one run significantly improves cache utilization and computational efficiency. On modern CPUs, vectorized instructions can be used to process multiple data points with a single CPU instruction

- Storing values that have the same data type together (e.g., numbers with other numbers, strings with other strings) offers a better compression ratio. We can use different compression algorithms depending on the data type and pick the most effective compression method for each case.

- To decide whether to use a column or a row-oriented store, you need to understand your **access patterns**. If the read data is consumed in records (i.e., most or all of the columns are requested) and the workload consists mostly of point queries and range scans, the row-oriented approach is likely to yield better results. If scans span many rows, or compute aggregate over a subset of columns, it is worth considering a column-oriented approach.

- Column-oriented databases should not be mixed up with wide column stores, such as *BigTable* or *HBase*, where data is represented as a multidimensional map, columns are grouped into column families (usually storing data of the same type), and inside each column family, data is stored row-wise. This layout is best for storing data retrieved by a key or a sequence of keys.

different NoSQL type data stores:
&nbsp;

<img src=../images/nosql-1.png title="nosql" width="600" height="400">

&nbsp;

- how BigTable stores data:
    - Bigtable stores data in scalable tables made up of columns and rows. Each table provides a sorted key/value map, with each row indexed by a single row key. Related columns are often grouped into column families and a unique name is assigned to each column family. The tables are also sparse; if a column does not contain data for a particular row, the cell does not use any storage space.

    - Within these tables, each row/column intersection can contain one or more cells. In a traditional relationship database table, each row/column intersection can contain only one cell. Every cell in a Bigtable table contains a unique timestamped version of the data. This approach enables Bigtable to maintain a record of how data has changed over time.
    
    - [more on BigTable](https://www.techtarget.com/searchdatamanagement/definition/Google-BigTable)

-