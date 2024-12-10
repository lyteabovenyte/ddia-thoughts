

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

- 