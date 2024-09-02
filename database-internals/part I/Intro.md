
<span style="color: #f2cf4a; font-family: Babas; font-size: 3em;">Database internals</span>

<span style="color: #f2cf4a; font-family: Babas; font-size: 2em;">Chapter 1</span>

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

###### wide-column stores:
<img src=../images/figure1-3.png title="wide-column" width="200" height="200">
<img src=../images/figure1-4.png title="wide-column" width="200" height="200">
&nbsp;

###### desciription on wide-column stores:
- Data is stored in a multidimensional sorted map with hierarchical indexes: we can locate the data related to a specific web page by its reversed URL and its contents or anchors by the timestamp. Each row is indexed by its row key. Related columns are grouped together in column families—contents and anchor in this example—which are stored on disk separately. Each column inside a column family is identified by the column key, which is a combination of the column family name and a qualifier (html, cnnsi.com, my.look.ca in this example). Column families store multiple versions of data by timestamp. This layout allows us to quickly locate the higher-level entries (web pages, in this case) and their parameters (versions of content and links to the other pages).

- how **BigTable** stores data: ( as a wide column store)
  - Bigtable stores data in scalable tables made up of columns and rows. Each table provides a sorted key/value map, with each row indexed by a single row key. Related columns are often grouped into column families and a unique name is assigned to each column family. The tables are also sparse; if a column does not contain data for a particular row, the cell does not use any storage space.

  - Within these tables, each row/column intersection can contain one or more cells. In a traditional relationship database table, each row/column intersection can contain only one cell. Every cell in a Bigtable table contains a unique timestamped version of the data. This approach enables Bigtable to maintain a record of how data has changed over time.
  
  - [more on BigTable](https://www.techtarget.com/searchdatamanagement/definition/Google-BigTable)


- Database systems store data records, consisting of multiple fields, in tables, where each table is usually represented as a separate file. Each record in the table can be looked up using a *search key*. To locate a record, database systems use *indexes*: auxiliary data structures that allow it to efficiently locate data records without scanning an entire table on every access. Indexes are built using a subset of fields identifying the record.

- A database system usually separates data files and index files: data files store data records, while index files store record metadata and use it to locate records in data files. Index files are typically smaller than the data files. Files are partitioned into pages, which typically have the size of a single or multiple disk blocks. Pages can be organized as sequences of records or as a *slotted pages*.
New records (insertions) and updates to the existing records are represented by key/value pairs. Most modern storage systems do not delete data from pages explicitly. Instead, they use deletion markers (also called *tombstones*), which contain deletion metadata, such as a key and a timestamp. Space occupied by the records shadowed by their updates or deletion markers is reclaimed during *garbage collection*, which reads the pages, writes the live (i.e., nonshadowed) records to the new place, and discards the shadowed ones.

- Data files (sometimes called primary files) can be implemented as index- organized tables (IOT), heap-organized tables (heap files), or hash-organized tables (hashed files). 
    - heap-organized-files: Records in heap files are not required to follow any particular order, and most of the time they are placed in a write order. This way, no additional work or file reorganization is required when new pages are appended. Heap files require additional index structures, pointing to the locations where data records are stored, to make them searchable.
    - hashed-organized-files: In hashed files, records are stored in buckets, and the hash value of the key determines which bucket a record belongs to. Records in the bucket can be stored in append order or sorted by key to improve lookup speed.
    - index-organized-files: Index-organized tables (IOTs) store data records in the index itself. Since records are stored in key order, range scans in IOTs can be implemented by sequentially scanning its contents.

- Storing data records in the index allows us to reduce the number of disk seeks by at least one, since after traversing the index and locating the searched key, we do not have to address a separate file to find the associated data record.

- When records are stored in a separate file, index files hold data entries, uniquely identifying data records and containing enough information to locate them in the data file. For example, we can store file offsets (sometimes called row locators), locations of data records in the data file, or bucket IDs in the case of hash files. In index-organized tables, data entries hold actual data records.
  - I also implemented this approach in my [Mock_distributed_services repo](https://github.com/lyteabovenyte/Mock_distributed_services/tree/main/internal/log) for commit-log service. in case you want to grasp on an implementation of this approach.

##### primary index as an indirection

- There are different opinions in the database community on whether data records should be referenced directly (through file offset) or via the primary key index

  - Both approaches have their pros and cons and are better discussed in the scope of a complete implementation. By referencing data directly, we can reduce the number of disk seeks, but have to pay a cost of updating the pointers whenever the record is updated or relocated during a maintenance process. Using indirection in the form of a primary index allows us to reduce the cost of pointer updates, but has a higher cost on a read path.

- MySQL InnoDB uses a primary index and performs two lookups: one in the secondary index, and one in a primary index when performing a query. This adds an overhead of a primary index lookup instead of following the offset directly from the secondary index.

<img src=../images/figure1-6.png title="primary and secondary index"/>

&nbsp;

##### buffering, immutability and ordering
- A storage engine is based on some data structure. However, these structures do not describe the semantics of caching, recovery, transactionality, and other things that storage engines add on top of them.
- Storage structures have three common variables: they use buffering (or avoid using it), use immutable (or mutable) files, and store values in order (or out of order). Most of the distinctions and optimizations in storage structures discussed in this book are related to one of these three concepts.
- *Buffering*:
    - This defines whether or not the storage structure chooses to collect a certain amount of data in memory before putting it on disk. Of course, every on-disk structure has to use buffering to some degree, since the smallest unit of data transfer to and from the disk is a block, and it is desirable to write full blocks. Here, we’re talking about avoidable buffering, something storage engine implementers choose to do. One of the first optimizations we discuss in this book is adding in-memory buffers to B-Tree nodes to amortize I/O costs. However, this is not the only way we can apply buffering. For example, two-component LSM Trees, despite their similarities with B-Trees, use buffering in an entirely different way, and combine buffering with immutability.
- *Mutability (or Immutability)*:
    - This defines whether or not the storage structure reads parts of the file, updates them, and writes the updated results at the same location in the file. Immutable structures are append-only: once written, file contents are not modified. Instead, modifications are appended to the end of the file. There are other ways to implement immutability. One of them is copy-on-write, where the modified page, holding the updated version of the record, is written to the new location in the file, instead of its original location. Often the distinction between LSM and B-Trees is drawn as immutable against in-place update storage, but there are structures (for example, “Bw-Trees”) that are inspired by B-Trees but are immutable.
- *Ordering*:
    - This is defined as whether or not the data records are stored in the key order in the pages on disk. In other words, the keys that sort closely are stored in contiguous segments on disk. Ordering often defines whether or not we can efficiently scan the range of records, not only locate the individual data records. Storing data out of order (most often, in insertion order) opens up for some write-time optimizations. For example, Bitcask and WiscKey store data records directly in append-only files.

### <span style="color: #f2cf4a; font-family: Babas; font-size: 2em;">Summary</span>

In this chapter, we’ve discussed the architecture of a database management system and covered its primary components.
To highlight the importance of disk-based structures and their difference from in- memory ones, we discussed memory- and disk-based stores. We came to the conclusion that disk-based structures are important for both types of stores, but are used for different purposes.
To understand how access patterns influence database system design, we discussed column- and row-oriented database management systems and the primary factors that set them apart from each other. To start a conversation about how the data is stored, we covered data and index files.
Lastly, we introduced three core concepts: buffering, immutability, and ordering. We will use them throughout this book to highlight properties of the storage engines that use them.