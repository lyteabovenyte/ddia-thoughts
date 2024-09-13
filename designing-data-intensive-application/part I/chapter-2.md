###### Data Models and Query Languages

- **Data models** are perhaps the most important part of developing software, because they have such a profound effect: not only on how the software is written, but also on how we think about the *problem* that we are solving.

- As an application developer, you look at the real world (in which there are peo‐ ple, organizations, goods, actions, money flows, sensors, etc.) and model it in terms of objects or data structures, and APIs that manipulate those data struc‐ tures. Those structures are often specific to your application.

-  When you want to store those data structures, you express them in terms of a general-purpose data model, such as JSON or XML documents, tables in a rela‐ tional database, or a graph model.


- The engineers who built your database software decided on a way of representing that JSON/XML/relational/graph data in terms of bytes in memory, on disk, or on a network. The representation may allow the data to be queried, searched, manipulated, and processed in various ways.


- On yet lower levels, hardware engineers have figured out how to represent bytes in terms of electrical currents, pulses of light, magnetic fields, and more.


- In this chapter we will look at a range of general-purpose data models for data stor‐ age and querying

- The best-known data model today is probably that of SQL, based on the relational model, data is organized into relations (called tables in SQL), where each relation is an unordered collection of tuples (rows in SQL).

- transaction processing (entering sales or banking transactions, airline reservations, stock-keeping in warehouses) and batch processing (customer invoicing, payroll, reporting).

- **indexing**:
  - In simple terminology, an index maps search keys to corresponding data on disk by using different in-memory & on-disk data structures. 
  - ...

- One-to-many relationships forms a tree structure.
- relational scheme for *One-to-many* relatioship
- ![relational-database](../images/bill-gates-resume.png)
- tree scheme for *One-to-many* relationship
- ![tree-form-of-One-to-many-relationship](../images/one-to-many.png)


- *Semi-structured data* is not “in-between” structured and unstructured data. Instead, this is a form of structured data that does not conform to the structure schema of databases.

- Documents in a document database are roughly equivalent to the programming concept of an object. 

- Document databases reverted back to the hierarchical model in one aspect: storing nested records (one-to-many relationships) within their parent record rather than in a separate table.

- comparison between **relational database** and **document-oriented database**:
    - The main arguments in favor of the document data model are schema flexibility, bet‐ ter performance due to locality, and that for some applications it is closer to the data structures used by the application. The relational model counters by providing better support for joins, and many-to-one and many-to-many relationships.

- However, if your application does use many-to-many relationships, the document model becomes less appealing. It’s possible to reduce the need for joins by denormal‐ izing, but then the application code needs to do additional work to keep the denor‐ malized data consistent. Joins can be emulated in application code by making multiple requests to the database, but that also moves complexity into the application and is usually slower than a join performed by specialized code inside the database. In such cases, using a document model can lead to significantly more complex appli‐ cation code and worse performance 


- It’s not possible to say in general which data model leads to simpler application code; it depends on the kinds of relationships that exist between data items. For highly interconnected data, the document model is awkward, the relational model is accept‐ able, and graph models are the most natural.

- Document databases are sometimes called schemaless, but that’s misleading, as the code that reads the data usually assumes some kind of structure—i.e., there is an implicit schema, but it is not enforced by the database 

- Schema-on-read is similar to dynamic (runtime) type checking in programming lan‐ guages, whereas schema-on-write is similar to static (compile-time) type checking.

- A document is usually stored as a single continuous string, encoded as JSON, XML, or a binary variant thereof (such as MongoDB’s BSON).

-  If a database is able to handle document-like data and also perform relational queries on it, applications can use the combination of features that best fits their needs.


- In a declarative query language, like SQL or relational algebra, you just specify the pattern of the data you want—what conditions the results must meet, and how you want the data to be transformed (e.g., sorted, grouped, and aggregated)—but not how to achieve that goal.

- declarative languages often lend themselves to parallel execution.

----------

- json is a data encoding format and it represent the tree structure of the one-to-many relationship between data, explicit

**normalization process on a database** means reducing the duplicate values by using a single identifier and store it on just one place
- As a rule of thumb, if you’re duplicating values that could be stored in just one place, the schema is not normalized.
    - The advantage of using an ID is that because it has no meaning to humans, it never needs to change: the ID can remain the same, even if the information it identifies changes. Anything that is meaningful to humans may need to change sometime in the future—and if that information is duplicated, all the redundant copies need to be updated. That incurs write overheads, and risks inconsistencies (where some copies of the information are updated but others aren’t). Removing such duplication is the key idea behind normalization in databases.

- Like document databases, IMS worked well for one-to-many relationships, but it made many-to-many relationships difficult, and it didn’t support joins. Developers had to decide whether to duplicate (denormalize) data or to manually resolve refer‐ ences from one record to another. These problems of the 1960s and ’70s were very much like the problems that developers are running into with document databases today.
    - document model and hierarchial model are best for one to many and are lacking in many-to-many relationships. --> could we wonder why?

- a **document** is usually stored as a continuous string, encoded as JSON, XML, or a binary variant such as MongoDB's BSON type
- The locality advantage only applies if you need *large* parts of the document at the same time. more on this below
    - The locality advantage only applies if you need large parts of the document at the same time. The database typically needs to load the entire document, even if you access only a small portion of it, which can be wasteful on large documents. On updates to a document, the entire document usually needs to be rewritten—only modifications that don’t change the encoded size of a document can easily be performed in place. For these reasons, it is generally recommended that you keep documents fairly small and avoid writes that increase the size of a document. These performance limitations significantly reduce the set of situations in which document databases are useful.
- [data locality](https://www.sciencedirect.com/topics/computer-science/data-locality#:~:text=In%20subject%20area%3A%20Computer%20Science,privacy%20laws%20and%20jurisdictional%20considerations.): Data locality refers to the concept of ensuring that data is stored and processed in a specific location. but in this part of the book and our discussion, data locality refers to grouping related data together for locality and ease of access to the whole data.
###### data locality research clauses:
 data locality refers to moving compute to data which is typically faster than moving data to compute. In Hadoop, data is divided into blocks and distributed across multiple servers (nodes). Additionally, it is replicated (typically 3 copies total) across these nodes. Thus, subsets of a dataset are distributed across nodes. When a map-reduce or Tez job is started, a container with code is distributed across the cluster nodes. These containers operate on the data in parallel and usually grab data blocks that are stored on the same node, thus achieving parallel processing with data locality. This results in fast overall execution of the full data set distributed across multiple nodes. This is key to operating on large volumes of data ... parallel processing is one component and processing data stored locally is another. Processing data that has to move across the network (no data locality) is slower.
Note that in cloud computing it is often advantageous NOT to have data locality. Local disks in the cloud are ephemeral ... if the (virtual) server is destroyed all data sitting on it are destroyed. Thus, putting data on local disks means you lose it when you spin down a cluster. One of the advantages to cloud is paying for servers only when you use them. Thus it is common to have scenarios when you spin up a cluster, do some processing and then spin it down (e.g. running a report or training a model in data science). In this scenario you would want your data stored on non-local data like AWS S3 object storage which is very inexpensive. This data persists separately from your cluster so only your compute is ephemeral. When you spin up a cluster, it reads from the permanent non-local storage and perhaps writes to it. You lose data locality but you gain the ability to pay for your cluster only when you use it. Compute on non-local data in this scenario is slower than local but not extremely so, especially when you scale out your cluster (more nodes) to increase the parallel processing aspect.

- *column-family* in bigTable data model, *multi-table index cluster table* in oracle are some approaches to the locality in relational data models.
- a good question to ask for whether to use document-based or relational data models is --> do we need data locality and do we need a large amount of a sepcific record at once or not, do we have a lot of *Joins* happening or we just have alot of many-to-one and many-to-many relationship (eventhough MongoDB Drivers automatically resolve database references in Json like objects and data.)

- declarative languages often lend themselves to parallel execution. 
- The **Document Object Model** (DOM) is the data representation of the objects that comprise the structure and content of a document on the web

###### MapReduce
- MapReduce is a programming model for processing large amounts of data in bulk across many machines, popularized by Google. A limited form of MapReduce is supported by some NoSQL datastores, including MongoDB and CouchDB, as a mechanism for performing read-only queries across many documents. more on MapReduce on [Coursera](https://www.coursera.org/articles/what-is-mapreduce) and on [databricks](https://www.databricks.com/glossary/mapreduce)
- an example of MapReduce support on MongoDB:
<img src=../images/mapReduce-mongodb.png>

- The **map** and **reduce** functions are somewhat restricted in what they are allowed to do. They must be *pure* functions, which means they only use the data that is passed to them as input, they cannot perform additional database queries, and they must not have any side effects. These restrictions allow the database to run the functions anywhere, in any order, and rerun them on failure. However, they are nevertheless powerful: *they can parse strings, call library functions, perform calculations*, and more.

###### Graph-like data models:
- There are several different, but related, ways of structuring and querying data in graphs. In this section we will discuss the property graph model (implemented by Neo4j, Titan, and InfiniteGraph) and the triple-store model (implemented by Datomic, AllegroGraph, and others).
- In Cypher, :WITHIN*0.. expresses that fact very concisely: it means “follow a WITHIN edge, zero or more times.” It is like the * operator in a regular expression.
Since SQL:1999, this idea of variable-length traversal paths in a query can be expressed using something called *recursive common table expressions* (the WITH RECURSIVE syntax)
    
 
```sql
WITH RECURSIVE
  -- in_usa is the set of vertex IDs of all locations within the United States
in_usa(vertex_id) AS (
SELECT vertex_id FROM vertices WHERE properties->>'name' = 'United States'
UNION
SELECT edges.tail_vertex FROM edges
JOIN in_usa ON edges.head_vertex = in_usa.vertex_id
WHERE edges.label = 'within' ),

-- in_europe is the set of vertex IDs of all locations within Europe
in_europe(vertex_id) AS (
SELECT vertex_id FROM vertices WHERE properties->>'name' = 'Europe'
UNION
SELECT edges.tail_vertex FROM edges
JOIN in_europe ON edges.head_vertex = in_europe.vertex_id
WHERE edges.label = 'within' ),

-- born_in_usa is the set of vertex IDs of all people born in the US
born_in_usa(vertex_id) AS (
SELECT edges.tail_vertex FROM edges
JOIN in_usa ON edges.head_vertex = in_usa.vertex_id
WHERE edges.label = 'born_in' ),

-- lives_in_europe is the set of vertex IDs of all people living in Europe
lives_in_europe(vertex_id) AS ( SELECT edges.tail_vertex FROM edges
JOIN in_europe ON edges.head_vertex = in_europe.vertex_id
WHERE edges.label = 'lives_in' )
SELECT vertices.properties->>'name'
FROM vertices

-- join to find those people who were both born in the US *and* live in Europe JOIN born_in_usa ON vertices.vertex_id = born_in_usa.vertex_id
JOIN lives_in_europe ON vertices.vertex_id = lives_in_europe.vertex_id;
```

same query with cypher query language of neo4j:

```js
MATCH
(person) -[:BORN_IN]->  () -[:WITHIN*0..]-> (us:Location {name:'United States'}),
(person) -[:LIVES_IN]-> () -[:WITHIN*0..]-> (eu:Location {name:'Europe'}) RETURN person.name
```

###### semantic-web and RDF
- The semantic web is fundamentally a simple and reasonable idea: websites already publish information as text and pictures for humans to read, so why don’t they also publish information as machine-readable data for computers to read? The *Resource Description Framework (RDF)* was intended as a mechanism for different websites to publish data in a consistent format, allowing data from different websites to be automatically combined into a *web of data*, a kind of internet-wide “database of everything.”

- *Triples* can be a good internal data model for applications, even if you have no interest in publishing RDF data on the semantic web.


```js
(person) -[:BORN_IN]-> () -[:WITHIN*0..]-> (location)
```

```saprql
?person :bornIn / :within* ?location.
```

- *SPARQL* is a nice query language—even if the semantic web never happens, it can be a powerful tool for applications to use internally.

###### the foundation: **DataLog**:
- The Datalog approach requires a different kind of thinking to the other query lan‐ guages discussed in this chapter, but it’s a very powerful approach, because rules can be combined and reused in different queries. It’s less convenient for simple one-off queries, but it can cope better if your data is complex.


### Summary
Historically, data started out being represented as one big tree (the hierarchical model), but that wasn’t good for representing many-to-many relationships, so the relational model was invented to solve that problem. More recently, developers found that some applications don’t fit well in the relational model either. New nonrelational “NoSQL” datastores have diverged in two main directions:
- 1.  *Document databases* target use cases where data comes in self-contained docu‐ ments and relationships between one document and another are *rare*.
- 2. *Graph databases* go in the opposite direction, targeting use cases where anything is potentially related to everything.

One thing that document and graph databases have in common is that they typically don’t enforce a schema for the data they store, which can make it easier to adapt applications to changing requirements. However, your application most likely still assumes that data has a certain structure; it’s just a question of whether the schema is explicit (enforced on *write*) or implicit (handled on *read*).

Each data model comes with its own query language or framework, and we discussed several examples: SQL, MapReduce, MongoDB’s aggregation pipeline, Cypher, SPARQL, and Datalog. We also touched on CSS and XSL/XPath, which aren’t data‐ base query languages but have interesting parallels.