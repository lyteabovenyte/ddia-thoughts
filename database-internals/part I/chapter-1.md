- [Preview of Database-Internals book](#preview-of-database-internals-book)
  - [Chapter 1](#chapter-1)
- [questions to choose the best database for individual need:](#questions-to-choose-the-best-database-for-individual-need)
  - [how to answer them:](#how-to-answer-them)

##### Preview of Database-Internals book
###### Chapter 1

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

Architecture of database management systems:  

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

- 