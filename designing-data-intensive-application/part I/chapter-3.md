##### Storage and Retrieval

###### intro:

Why should you, as an application developer, care how the database handles storage and retrieval internally? You’re probably not going to implement your own storage engine from scratch, but you *do* need to select a storage engine that is appropriate for your application, from the many that are available. In order to tune a storage engine to perform well on your kind of workload, you need to have a rough idea of what the storage engine is doing under the hood.

In particular, there is a big difference between storage engines that are optimized for *transactional workloads* and those that are optimized for *analytics*. We will explore that distinction later in “*Transaction Processing or Analytics*?”, and in “*Column-Oriented Storage*”, we’ll discuss a family of storage engines that is optimized for analytics.

However, first we’ll start this chapter by talking about storage engines that are used in the kinds of databases that you’re probably familiar with: traditional relational databases, and also most so-called NoSQL databases. We will examine two families of storage engines: *log-structured* storage engines, and *page-oriented* storage engines such as *B-trees*.

- "**Log**" is an append-only data file, as appending to a file is generally very efficient.

- Real databases have more issues to deal with these type of structure (*such as concurrency control, reclaiming disk space so that the log doesn’t grow forever, and handling errors and partially written records*), but the basic principle is the same. Logs are incredibly useful, and we will encounter them several times in the rest of this book.

- The word log is often used to refer to application logs, where an application outputs text that describes what’s happening. In this book, log is used in the more general sense: an append-only sequence of records. It doesn’t have to be human-readable; it might be binary and intended only for other programs to read.

- An **index** is an additional structure that is derived from the primary data. Many databases allow you to add and remove indexes, and this doesn’t affect the contents of the database; it only affects the performance of queries. Maintaining additional structures incurs overhead, especially on *writes*. For writes, it’s hard to beat the performance of simply appending to a file, because that’s the simplest possible write operation. Any kind of index usually slows down writes, because the index also needs to be updated every time data is written.

- in log-structured database, we only ever append to a file—so how do we avoid eventually running out of disk space? A good solution is to break the log into segments of a certain size by closing a segment file when it reaches a certain size, and making subsequent writes to a new segment file. We can then perform **compaction** on these segments. Compaction means throwing away duplicate keys in the log, and keeping only the most recent update for each key. Moreover, since compaction often makes segments much smaller (assuming that a key is overwritten several times on average within one segment), we can also merge several segments together at the same time as performing the compaction. Segments are never modified after they have been written, so the merged segment is written to a new file. The merging and compaction of frozen segments can be done in a background thread, and while it is going on, we can still continue to serve read and write requests as normal, using the old segment files. After the merging process is complete, we switch read requests to using the new merged segment instead of the old segments—and then the old segment files can simply be deleted.

- for better implementation of segment and stores on a structured-log design you can check my [repo](https://github.com/lyteabovenyte/Mock_distributed_services).

- [more on differences between log-structured storage engines and page oriented storage engines](https://edward-huang.com/distributed-system/database/2021/03/30/two-families-of-storage-engine-that-powers-modern-day-database/#:~:text=Log%2DStructured%20Segment%20Storage%20Engine,write%20is%20always%20in%20sequence.)

- [more on Comparative Study of Balanced Tree](https://medium.com/@nyx.code/comparative-study-of-balanced-tree-415207ac3ac5)

##### Constructing and maintaining SSTables

- We can now make our storage engine work as follows:
    - When a write comes in, add it to an in-memory balanced tree data structure (for
    example, a red-black tree). This in-memory tree is sometimes called a *memtable*.
    - When the memtable gets bigger than some threshold—typically a few megabytes —write it out to disk as an SSTable file. This can be done efficiently because the tree already maintains the key-value pairs sorted by key. The new SSTable file becomes the most recent segment of the database. While the SSTable is being written out to disk, writes can continue to a new memtable instance.
    - In order to serve a read request, first try to find the key in the memtable, then in the most recent on-disk segment, then in the next-older segment, etc.
    - From time to time, run a *merging* and *compaction* process in the background to combine segment files and to discard overwritten or deleted values.

- Fine so far—but how do you get your data to be sorted by key in the first place? Our incoming writes can occur in any order.
Maintaining a sorted structure on disk is possible (“B-Trees”), but maintaining it in memory is much easier. There are plenty of well-known tree data structures that you can use, such as red-black trees or AVL trees. With these data structures, you can insert keys in any order and read them back in sorted order.

- Lucene, an indexing engine for full-text search used by Elasticsearch and Solr, uses a similar method for storing its *term dictionary*. A full-text index is much more complex than a key-value index but is based on a similar idea: given a word in a search query, find all the documents (web pages, product descriptions, etc.) that mention the word. This is implemented with a key-value structure where the key is a word (a term) and the value is the list of IDs of all the documents that contain the word (the postings list). In Lucene, this mapping from term to postings list is kept in SSTable-like sorted files, which are merged in the background as needed.

- A *Bloom filter* is a memory-efficient data structure for approximating the contents of a set. It can tell you if a key does not appear in the database, and thus saves many unnecessary disk reads for nonexistent keys.

##### B-Tree:
- Like SSTables, B-trees keep key-value pairs sorted by key, which allows efficient key- value lookups and range queries. But that’s where the similarity ends: B-trees have a very different design philosophy.
- The log-structured indexes we saw earlier break the database down into variable-size segments, typically several megabytes or more in size, and always write a segment sequentially. By contrast, B-trees break the database down into fixed-size blocks or pages, traditionally 4 KB in size (sometimes bigger), and read or write one page at a time. This design corresponds more closely to the underlying hardware, as disks are also arranged in fixed-size blocks.
- Each page can be identified using an address or location, which allows one page to refer to another—similar to a pointer, but on disk instead of in memory. We can use these page references to construct a tree of pages

<img src=../images/b-tree.png>

- One page is designated as the root of the B-tree; whenever you want to look up a key in the index, you start here. The page contains several keys and references to child pages. Each child is responsible for a continuous range of keys, and the keys between the references indicate where the boundaries between those ranges lie.
- If you want to update the value for an existing key in a B-tree, you search for the leaf page containing that key, change the value in that page, and write the page back to disk (any references to that page remain valid). If you want to add a new key, you need to find the page whose range encompasses the new key and add it to that page. If there isn’t enough free space in the page to accommodate the new key, it is split into two half-full pages, and the parent page is updated to account for the new subdi‐ vision of key ranges

<img src=../images/update-b-tree.png>

###### making B-Trees reliable
- The basic underlying write operation of a B-tree is to overwrite a page on disk with new data. It is assumed that the overwrite does not change the location of the page; i.e., all references to that page remain intact when the page is overwritten. This is in stark contrast to log-structured indexes such as LSM-trees, which only append to files (and eventually delete obsolete files) but never modify files in place.
- 
