#### chapter 1. introduction

- A graph is a representation of a network,

- A **data model** organizes a set of data and establishes how different data elements relate to one another. Data models help organizations use their data effectively for decision making and other business needs. A relational model organizes data in terms of *tables*. A graph data model, on the other hand, views data as a graph, taking into account the *relationships* within the dataset. Graph data modeling is the process of describing a dataset to be able to use it in a graph database such as Neo4j, Azure Cosmos DB, or others. This process means deciding which elements within your dataset will be defined as nodes, edges, and properties. There is no one set way to do this, so graph data modeling involves some important decision making to ensure the graph is useful to its end users. 

- modeling graphs is only half the story. We might also want to *process* them to
reveal insight that isn’t immediately obvious. This is the domain of graph algorithms.

- Graph algorithms provide one of the most potent approaches to analyzing connected
data because their mathematical calculations are specifically built to operate on rela‐
tionships. They describe steps to be taken to process a graph to discover its general
qualities or specific quantities. Based on the mathematics of graph theory, graph algo‐
rithms use the relationships between nodes to infer the organization and dynamics of
complex systems. Network scientists use these algorithms to uncover hidden infor‐
mation, test hypotheses, and make predictions about behavior.

- Graph processing includes the methods by which graph workloads and tasks are car‐
ried out. Most graph queries consider specific parts of the graph (e.g., a starting
node), and the work is usually focused in the surrounding subgraph. We term this
type of work *graph local*, and it implies declaratively querying a graph’s structure. This type of graph-local processing is often utilized for real-time
transactions and pattern-based queries. When speaking about graph algorithms, we are typically looking for global patterns and structures. 
The input to the algorithm is usually the whole graph, and the output
can be an enriched graph or some aggregate value such as a score.

-  Graph pattern-based querying is often used for *local* data analysis, whereas
**graph computational algorithms** usually refer to more *global* and *iterative* analysis.
Although there is overlap in how these types of analysis can be employed, we use the
term **graph algorithms** to refer to the latter, more computational analytics and data
science uses.

- whole-graph operations are processed by computational algorithms and subgraph
operations are queried in databases.

- 