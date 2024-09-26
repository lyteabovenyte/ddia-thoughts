#### worth researching topics:

#### chapter 1:

the fundamental building block of a data-intensive application.

- allow users to search data by keyword or filter it in various ways. (search indexes)
- send a message to another process to handle it asynchronously (stream processing)

hardware faults:

- disks may be configured on RAID configuration. --> what exactly is RAID configuraion?

##### latency and response time:

- random additional latency could be introduced by a **context switch**? to a background process.
  A context switch is the process of storing the state of a process or thread, so that it can be restored and resume execution at a later point.[more on context switching](https://www.techtarget.com/whatis/definition/context-switch)

- **garbage collection**? pause

- calculating response time percentile on an **ongoing basis** for monitoring and metrics?

##### scalibility

- what should we do when in our application, the volume of write are high? how should we design our system to cop that?
- what about read? --> caching on writes and move the load from read to write at early stage
- what about data complexity?
- what about the amount of the data?
- what about the access patterns?

##### operability

- operational model? ( if I do X, Y will happen) on documentation or alongside it

##### complexity

- various possible symptoms of complexity:
  - explosion of the state space**??**

##### locality

- what is TTD (test driven development) --> surface knowledge is not enough. research more deeply.
  Agile community created TTD that is helpful when developing software in a frequently changing environment.
- what is _column-family_ in bigTable?

- what is refactoring

#### Query language for data:

- declarative languages often lend themselves to parallel execution. --> how can that be achieved?

##### semantic-web and RDF?

- semantic web and resouce description framework?
  The semantic web is fundamentally a simple and reasonable idea: websites already publish information as text and pictures for humans to read, so why don’t they also publish information as machine-readable data for computers to read? The Resource Description Framework (RDF) was intended as a mechanism for different web‐ sites to publish data in a consistent format, allowing data from different websites to be automatically combined into a web of data—a kind of internet-wide “database of everything.”

- how can "_Triples_ can be a good internal data model for applications, even if you have no interest in publishing RDF data on the semantic web." statement be considered?
- SPARQL is a query language for triple-stores using the RDF data model. (It is an acronym for SPARQL Protocol and RDF Query Language, pronounced “sparkle.”)

- "SPARQL is a nice query language—even if the semantic web never happens, it can be a powerful tool for applications to use internally." --> how??
- research on SPARQL --> how it behaves and how it is correlated to semantic web and RDF and how can we use it inside our application.

##### interesting topic and a full overview of the query languages we learned so far and worth researching them all at an exact session

Each data model comes with its own query language or framework, and we discussed several examples:
SQL, MapReduce, MongoDB’s aggregation pipeline, Cypher, SPARQL, and Datalog. We also touched on CSS and XSL/XPath,
which aren’t data‐ base query languages but have interesting parallels.

#### chapter 3:

- merge-sort, binary tree search, binary search and merge algorithms on data structure and their implementations on structured
  logs.
- sorted balanced-trees such as AVL and red-black tree and B-tree.
- a diverse research on _column-oriented_ storage

#### chapter 4, encoding evolution



#### chapter 9:
- two event are ordered if they are causaly related. (one happened before another or after, the before-after relationship makes causality)
- the advantage of lamport timestamp over version vector is that they are more compact.
- how can we prevent dirty write and what is dirty write? actually, it need so much more research and detail that how theread committed prevent this?
- 