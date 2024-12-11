

#### chapter 2. Representing network structure: Designing your first graph model
- There are several categories
of graph algorithms. For example, you can use centrality algorithms to identify the
most important or influential nodes. On the other hand, you can use community
detection or clustering algorithms to recognize highly interconnected groups of
nodes. One interesting fact about graph algorithms is that most have a predetermined
graph shape that works best as an input. In practice, you do not adjust graph algo-
rithms to fit your data but, rather, transform your data to provide the correct algorithm
input. You will learn that most centrality and community detection algorithms were
designed to take a graph with a single node and relationship type as input; however,
you will frequently deal with graphs with multiple node or relationship types. There-
fore, you need to learn techniques that will help you
shapes expected by the graph algorithms.
so the transformation phase could be to shape the graph to fit the algorithm needs.

-  in a *bipartite* graph, the relationships always start from one set of
nodes and end at the other.

- **how to store undirected relationships in the next section**.?

- the most famous graph algorithm to evaluate the *transitive* influence of a
node is `PageRank`.

-  most inferred similarity networks are undirected and weighted,

- common theme in network analysis is to translate
indirect graph patterns and relationships to direct ones. Using a graph database
with a dedicated graph-pattern query language makes it easier for you to instantiate
those network transformations. For example, you can translate the retweet links
between tweets to direct amplification relationships between users. Another frequent
scenario is translating a bipartite network to a monopartite network. ***Monopartite projections*** are used because most graph algorithms are designed to work on networks
with a single type of nodes and relationships

- Reducing indirect graph patterns to direct relationships (**monopartite projection**) is a frequent step in network analysis.

### Part II

- how to characterize a network by examining a social graph of Twit-
ter followers. 

- A significant part of graph analysis involves the transformation
of a graph to align with the structure the graph algorithms expect.
so we should learn what the graph algo expect and be ready to satisfy that.

#### chapter 3. Your first steps with Cypher query language

- Each relationship has a single type.

- inline pattern for `MATCH` a pattern, which we will describe the node with it's label and it's properties

- A WHERE clause can only exist when it follows a `WITH`, a `MATCH`, or an
`OPTIONAL MATCH`clause. When you have many MATCH or WITH clauses in sequence, 
make sure to append the WHERE clause after each of them, where
needed. You might sometimes get the same results even if you only use a sin-
gle WHERE clause after multiple MATCH statements, but the query performance
will most likely be worse.

- The MATCH clause is often used to find existing nodes or relationships in the database
and then insert additional data with a `CREATE` or `MERGE` clause

- It is intuitive that when you try to retrieve a nonexistent graph pattern from the data-
base, you will get no results. What is not so intuitive is that when you have multiple
MATCH clauses in sequence, if only a single MATCH clause tries to retrieve a nonexistent
pattern from the database, the **whole** query will return no results.

- If you do not want your query to stop when a single MATCH clause finds no existing
graph patterns in the database, you can use the `OPTIONAL MATCH` clause. The OPTIONAL
MATCH clause would return a null value if no matching patterns were found in the data-
base instead of returning no results, behaving similarly to an `OUTER JOIN` in SQL.

- The OPTIONAL MATCH can also be used to retrieve node relationships *if* they exist.

- Using the `WITH` clause, you can manipulate the data as an intermediate step before
passing the results to the next part of the Cypher query. The intermediate data manipulations 
within a Cypher statement before passing them on to the next part can be
one or more of the following:
    - Filter results
    - Select results
    - Aggregate results
    - Paginate results
    - Limit results

- A `SET` clause is used to update labels of nodes and properties of both nodes and rela-
tionships. The SET clause is very often used in combination with the MATCH clause to
update existing node or relationship properties. There is also a special syntax for a SET clause to change or mutate many properties
using a map data structure.

- Multiple node labels are helpful when you want to tag your nodes for faster and easier
retrieval.

- The `REMOVE` clause is the opposite of the SET clause. It is used to remove node labels
and node and relationship properties. Removing a node property can also be under-
stood as setting its value to null

- the `DELETE` clause is used to delete nodes and relationships in the database. 

- As deleting nodes with existing relationships is a frequent procedure, Cypher query
language provides a `DETACH DELETE` clause that first deletes all the relationships
attached to a node and then deletes the node itself.

- The `MERGE` clause can be understood as a combination of using both the MATCH and
CREATE clauses. Using the MERGE clause, you instruct the query engine first to try to
match a given graph pattern, and if it does not exist, it should then create the pattern

- The MERGE clause only supports inline graph pattern description and **cannot** be used
in combination with a WHERE clause.

- A statement that can be rerun multiple times and always output
the same results is also known as an **idempotent** statement. When you import data into
the graph database, it is advisable to use the MERGE instead of the CREATE clause

- When designing a graph model, the best practice is to define a unique identifier
for each node label. A unique identifier consists of defining a unique property
value for each node in the graph

- A MERGE clause can be followed by an optional ON CREATE SET and ON MATCH SET. 
```sql
MERGE (t:Person {name: "amir"})
ON CREATE SET t.createdAt = datetime() -- if it does not exist, this will be invoked.
ON MATCH SET t.updatedAt = datetime() -- if it exists this will be invoked.
```
- When creating or importing data to Neo4j, you typically want to split a
Cypher statement into multiple MERGE clauses and merge nodes and relation-
ships separately to enhance performance. When merging nodes, the best
approach is to include only the node’s unique identifier property in the
MERGE clause and add additional node properties with the ON MATCH SET or ON
CREATE SET clauses.

- Handling relationships is a bit different. If there can be at most a single
relationship of one type between two nodes, like in the FRIEND example, then
do not include any relationship properties in the MERGE clause. Instead, use
the ON CREATE SET or ON MATCH SET clauses to set any relationship properties.
However, if your graph model contains multiple relationships of the same
type between a pair of nodes, then use only the unique identifier property of
the relationship in the MERGE statement and set any additional properties the
same as previously discussed

- The Neo4j graph database model is considered **schemaless**, meaning you can add any
type of nodes and relationships without defining the graph schema model. There are,
however, some *constraints* you can add to your graph model to ensure data integrity.

- You can think of unique constraints as a concept 
similar to primary keys in relational databases, although the difference is that
Neo4j’s unique constraint allows null values. it create an index on that unique constraint and
make the query performance better in some regards.

- defining constraint on a label and a unique property:
```sql
CREATE CONSTRAINT IF NOT EXISTS FOR (u:User) REQUIRE u.id IS UNIQUE;
CREATE CONSTRAINT IF NOT EXISTS FOR (p:Tweet) REQUIRE p.id IS UNIQUE;
```

- The Cypher query language has a `LOAD CSV` clause that enables you to open and
retrieve information from CSV files. The LOAD CSV clause can fetch local CSV files as
well as CSV files from the internet. The LOAD CSV clause can load
CSV files whether or not they contain a header. If the header is present, each row of
the CSV file will be available as a *map* data structure that can be used later in the
query. Conversely, when there is no header present, the rows will be available as *lists*.
The LOAD CSV clause can also be used in combination with a `FIELDTERMINATOR` clause
to set a custom delimiter, where you are, for example, dealing with a tab-separated
value format.

- It is important to note that the LOAD CSV clause returns all values as **strings** and
makes no attempt to identify data types. You must convert the values to the correct
data type in your Cypher import statements.

- the general rule of thumb is to split the import queries by *node labels* and
*relationship types* as much as possible.

- To define that the import should be split into multiple transactions, use the
`CALL {}` clause to define a Cypher subquery that has the `IN TRANSACTIONS` clause
appended. Any variables used in a Cypher subquery from the enclosing query must be
explicitly defined and imported using the `WITH` clause. The query steps in the Cypher
subquery will be executed for every row in the CSV file. (batch mode imports)

- When you are importing any relationship into the database,
you will most likely **match** or **merge** both source and target nodes and then connect
them

- 