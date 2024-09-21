#### chapter 7
##### Transactions
###### one of the great things besides the hundreds of those, is that you can also see martin explaining things (including in this book plus other things) on another platform and webinars. for this chapter there is a [40 minute talk by *martin kelppmann*](https://youtu.be/5ZjhNTM8XU8?si=dYqnSBn-CRFrq9JV) that you can enjoy.


##### intro
- A **transaction** is a way for an application to group several reads and writes together into a *logical unit*. Conceptually, all the reads and writes in a transaction are executed as *one* operation: either the entire transaction succeeds (*commit*) or it fails (*abort*, *rollback*). If it fails, the application can safely retry. With transactions, error handling becomes much simpler for an application, because it doesn’t need to worry about partial failure—i.e., the case where some operations succeed and some fail (for whatever reason).
- Transactions are not a law of nature; they were created with a purpose, namely to *simplify the programming model for applications accessing a database*. By using transactions, the application is free to ignore certain potential error scenarios and concurrency issues, because the database takes care of them instead (we call these *safety guarantees*).
- Not every application needs transactions, and sometimes there are advantages to weakening transactional guarantees or abandoning them entirely (for example, to achieve higher performance or higher availability). Some safety properties can be achieved without transactions.
- safety guarantees --> the safeness that the database offers and the application can ignore and pass them to the database itself.
- In this chapter, we will examine many examples of things that can go wrong, and explore the algorithms that databases use to guard against those issues. We will go especially deep in the area of *concurrency control*, discussing various kinds of race conditions that can occur and how databases implement *isolation levels such as read committed, snapshot isolation, and serializability*.

### The meaning of ACID
- The safety guarantees provided by transactions are often described by the well-known acronym ACID, which stands for *Atomicity, Consistency, Isolation, and Durability*.
- Systems that do not meet the ACID criteria are sometimes called *BASE*, which stands for *Basically Available, Soft state, and Eventual consistency*


##### Atomicity: (fault handling: deadlock, network fault, constraint violation, crash --> all goes aborted)
- in *multi-threaded* programming, if one thread executes an atomic operation, that means there is no way that another thread could see the half-finished result of the operation. The system can only be in the state it was *before* the operation or *after* the operation, not something in between.
- in the context of ACID, atomicity is **not** about *concurrency*. It does not describe what happens if several processes try to access the same data at the same time, because that is covered under the letter *I*, for **isolation** 
- ACID atomicity describes what happens if a client wants to make several writes, but a fault occurs after some of the writes have been processed—for example, a process crashes, a network connection is interrupted, a disk becomes full, or some integrity constraint is violated. If the writes are grouped together into an atomic transaction, and the transaction cannot be completed (*committed*) due to a fault, then the transaction is **aborted** and the database must discard or undo any writes it has made so far in that transaction.
- if a transaction was aborted, the application can be sure that it didn’t change anything, so it can safely be retried.
- Perhaps *abortability* would have been a better term than *atomicity*

##### Consistency:
- The word consistency is terribly overloaded:
  - In Chapter 5 we discussed *replica consistency* and the issue of *eventual consistency* that arises in asynchronously replicated systems
  - *Consistent hashing* is an approach to partitioning that some systems use for rebalancing
  - In the *CAP theorem* (see Chapter 9), the word consistency is used to mean *linearizability*
  - In the context of ACID, consistency refers to an **application-specific notion** of the database being in a “good state.”
- The idea of ACID consistency is that you have certain statements about your data (*invariants*) that must always be true—for example, in an accounting system, credits and debits across all accounts must always be balanced. If a transaction starts with a database that is valid according to these invariants, and any writes during the transaction preserve the validity, then you can be sure that the invariants are always satisfied. However, this idea of consistency depends on the application’s notion of invariants, and it’s the application’s responsibility to define its transactions correctly so that they preserve consistency. This is not something that the database can guarantee: if you write bad data that violates your invariants, the database can’t stop you. (Some spe‐ cific kinds of invariants can be checked by the database, for example using foreign key constraints or uniqueness constraints. However, in general, **the application defines what data is valid or invalid—the database only stores it**.)
- Atomicity, isolation, and durability are properties of the database, whereas consis‐ tency (in the ACID sense) is a property of the application. The application may rely on the database’s *atomicity* and *isolation* properties in order to achieve consistency, but it’s not up to the database alone. Thus, the letter C doesn’t really belong in ACID.

##### Isolation:
- *Isolation* in the sense of ACID means that concurrently executing transactions are isolated from each other: they cannot step on each other’s toes. The classic database textbooks formalize isolation as *serializability*, which means that each transaction can pretend that it is the only transaction running on the entire database. The database ensures that when the transactions have committed, the result is the same as if they had run **serially** (one after another), even though in reality they may have run concurrently. 
- However, in practice, serializable isolation is rarely used, because it carries a performance penalty. Some popular databases, such as Oracle 11g, don’t even implement it. In Oracle there is an isolation level called *“serializable*,” but it actually implements something called **snapshot isolation**, which is a weaker guarantee than serializability.

##### Durability:
- The purpose of a database system is to provide a safe place where data can be stored without fear of losing it. Durability is the promise that once a transaction has com‐ mitted successfully, any data it has written will not be forgotten, even if there is a hardware fault or the database crashes.
- In a single-node database, durability typically means that the data has been written to nonvolatile storage such as a hard drive or SSD. It usually also involves a *write-ahead log* or similar, which allows recovery in the event that the data structures on disk are corrupted. In a replicated database, durability may mean that the data has been successfully copied to some number of nodes. In order to provide a durability guarantee, a database must wait until these writes or replications are complete before reporting a transaction as successfully committed.
- **perfect durability does not exist**

### Single Object and Multi-Object Operations:
- To recap, in ACID, atomicity and isolation describe what the database should do if a client makes several writes within the same transaction:
  - **Atomicity** (*abortability in the case of a fault*): If an error occurs halfway through a sequence of writes, the transaction should be aborted, and the writes made up to that point should be discarded. In other words, the database saves you from having to worry about partial failure, by giv‐ ing an all-or-nothing guarantee.
  - <img src=../images/7-3.png>
  - **Isolation**: Concurrently running transactions shouldn’t interfere with each other. For example, if one transaction makes several writes, then another transaction should see either all or none of those writes, but not some subset.
  - <img src=../images/7-2.png>

- *Multi-object transactions* require some way of determining which read and write operations belong to the same transaction. In relational databases, that is typically done based on the client’s TCP connection to the database server: on any particular connection, everything between a BEGIN TRANSACTION and a COMMIT statement is considered to be part of the same transaction.
###### Single-object writes
-  Atomicity can be implemented using a *log* for crash recovery, and isolation can be implemented using a *lock* on each object (allowing only one thread to access an object at any one time).
- Some databases also provide more complex atomic operations,iv such as an increment operation, which removes the need for a *read-modify-write* cycle Similarly popular is a *compare-and-set* operation, which allows a write to happen *only if* the value has not been concurrently changed by someone else
- although a *transaction* is usually understood as a mechanism for grouping multiple operations on multiple objects into one unit of execution.

##### The need for multi-object transactions
- There are some use cases in which single-object inserts, updates, and deletes are suffi‐ cient. However, in many other cases writes to several different objects need to be coordinated:
  -  In a relational data model, a row in one table often has a foreign key reference to a row in another table. (Similarly, in a graph-like data model, a vertex has edges to other vertices.) Multi-object transactions allow you to ensure that these refer‐ ences remain valid: when inserting several records that refer to one another, the foreign keys have to be correct and up to date, or the data becomes nonsensical.
  -  In a document data model, the fields that need to be updated together are often within the same document, which is treated as a single object—no multi-object transactions are needed when updating a single document. However, document databases lacking join functionality also encourage *denormalization*. When denormalized information needs to be updated, you need to update several documents in one go. Transactions are very useful in this situation to prevent denormalized data from going out of sync.
  - In databases with secondary indexes (almost everything except pure key-value stores), the indexes also need to be updated every time you change a value. These indexes are different database objects from a transaction point of view: for example, without transaction isolation, it’s possible for a record to appear in one index but not another, because the update to the second index hasn’t happened yet.
###### Handling errors and aborts
- A key feature of a transaction is that it can be aborted and safely retried if an error occurred.
- Errors will inevitably happen, but many software developers prefer to think only about the happy path rather than the intricacies of error handling. For example, pop‐ ular object-relational mapping (ORM) frameworks such as Rails’s ActiveRecord and Django don’t retry aborted transactions—the error usually results in an exception bubbling up the stack, so any user input is thrown away and the user gets an error message. This is a shame, because the whole point of aborts is to enable safe retries.
- Although retrying an aborted transaction is a simple and effective error handling mechanism, it isn’t perfect:
  - If the transaction actually succeeded, but the network failed while the server tried to acknowledge the successful commit to the client (so the client thinks it failed), then retrying the transaction causes it to be performed twice—unless you have an additional application-level deduplication mechanism in place.
  - If the error is due to overload, retrying the transaction will make the problem worse, not better. To avoid such feedback cycles, you can limit the number of retries, use exponential backoff, and handle overload-related errors differently from other errors (if possible).
  - It is only worth retrying after transient errors (for example due to deadlock, iso‐ lation violation, temporary network interruptions, and failover); after a perma‐ nent error (e.g., constraint violation) a retry would be pointless.
  - If the transaction also has side effects outside of the database, those side effects may happen even if the transaction is aborted. For example, if you’re sending an email, you wouldn’t want to send the email again every time you retry the trans‐ action. If you want to make sure that several different systems either commit or abort together, *two-phase commit* can help
  - If the client process fails while retrying, any data it was trying to write to the database is lost.

### Weak Isolation Levels
- If two transactions don’t touch the same data, they can safely be run in parallel, because neither depends on the other. Concurrency issues (race conditions) only come into play when one transaction reads data that is concurrently modified by another transaction, or when two transactions try to simultaneously modify the same data.
- databases have long tried to hide concurrency issues from application developers by providing *transaction isolation*. In theory, isolation should make your life easier by letting you pretend that no concurrency is happening: **serializable isolation** means that the database guarantees that transactions have the same effect as if they ran *serially* (i.e., one at a time, without any concurrency).
- In practice, isolation is unfortunately not that simple. Serializable isolation has a *performance cost*, and many databases don’t want to pay that price. It’s therefore common for systems to use weaker levels of isolation, which protect against *some* concurrency issues, but not all. Those levels of isolation are much harder to understand, and they can lead to subtle bugs, but they are nevertheless used in practice
- Rather than blindly relying on tools, we need to develop a good understanding of the kinds of *concurrency problems* that exist, and how to prevent them. Then we can build applications that are reliable and correct, using the tools at our disposal.
- In this section we will look at several weak (*nonserializable*) isolation levels that are used in practice, and discuss in detail what kinds of race conditions can and cannot occur, so that you can decide what level is appropriate to your application. Once we’ve done that, we will discuss serializability in detail.
- <img src=../images/yt-ch7-1.png height=400>
#### Read Commited (preventing dirty read and dirty writes)
- The most basic level of transaction isolation is read committed. It makes two guarantees:
  - When reading from the database, you will only see data that has been committed (no *dirty reads*).
  - When writing to the database, you will only overwrite data that has been committed (no *dirty writes*).
- No dirty reads: Imagine a transaction has written some data to the database, but the transaction has not yet committed or aborted. Can another transaction see that uncommitted data? If yes, that is called a *dirty read*. 
- No dirty writes: What happens if two transactions concurrently try to update the same object in a database? We don’t know in which order the writes will happen, but we normally assume that the later write overwrites the earlier write. However, what happens if the earlier write is part of a transaction that has not yet committed, so the later write overwrites an uncommitted value? This is called a dirty write. Transactions running at the read committed isolation level must prevent dirty writes, usually by delaying the second write until the first write’s transaction has committed or aborted.
- ###### implementing read commmited:
  - Most commonly, databases prevent dirty writes by using row-level locks: when a transaction wants to modify a particular object (row or document), it must first acquire a lock on that object. It must then hold that lock until the transaction is committed or aborted. Only one transaction can hold the lock for any given object; if another transaction wants to write to the same object, it must wait until the first transaction is committed or aborted before it can acquire the lock and continue. This locking is done automatically by databases in read committed mode (or stronger iso‐ lation levels).
  - Most databases prevent dirty reads using this approach: for every object that is written, the database remembers both the *old* committed value and the *new* value set by the transaction that currently holds the write lock. While the transaction is ongoing, any other transactions that read the object are simply given the old value. Only when the new value is committed do transactions switch over to reading the new value.

#### Snapshot Isolation and Repeatable Read
- 