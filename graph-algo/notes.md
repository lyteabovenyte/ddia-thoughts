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

- **Online transaction processing (OLTP)** operations are typically short activities like
booking a ticket, crediting an account, booking a sale, and so forth. OLTP implies
voluminous low-latency query processing and high data integrity. Although OLTP
may involve only a small number of records per transaction, systems process many
transactions concurrently.

- **Online analytical processing (OLAP)** facilitates more complex queries and analysis
over historical data. These analyses may include multiple data sources, formats, and
types. Detecting trends, conducting “what-if ” scenarios, making predictions, and
uncovering structural patterns are typical OLAP use cases. Compared to OLTP,
OLAP systems process fewer but longer-running transactions over many records.
OLAP systems are biased toward faster reading without the expectation of transac‐
tional updates found in OLTP, and batch-oriented operation is common.

- Bringing together analytics and transactions enables *continual analysis* as a natural
part of regular operations. As data is gathered—from point-of-sale (POS) machines,
manufacturing systems, or internet of things (IoT) devices—analytics now supports
the ability to make real-time recommendations and decisions while processing. This
trend was observed several years ago, and terms to describe this merging include
**translytics** and **hybrid transactional and analytical processing (HTAP)**.

- According to Gartner: [HTAP](https://www.snowflake.com/guides/htap-hybrid-transactional-and-analytical-processing/) could potentially redefine the way some business processes are executed, as
real-time advanced analytics (for example, planning, forecasting and what-if analysis)
becomes an integral part of the process itself, rather than a separate activity performed
after the fact. This would enable new forms of real-time business-driven decision-
making process. Ultimately, HTAP will become a key enabling architecture for intelli‐
gent business operations.

- As OLTP and OLAP become more integrated and begin to support functionality pre‐
viously offered in only one silo, it’s no longer necessary to use different data products
or systems for these workloads—we can simplify our architecture by using the same
platform for both. This means our analytical queries can take advantage of real-time
data and we can **streamline** the iterative process of analysis.

- **Preferential attachment** is the phenomenon where the more connected a
node is, the more likely it is to receive new links. This leads to uneven concentrations and
hubs.

- A **power law** (also called a *scaling law*) describes the relationship between two quanti‐
ties where one quantity varies as a power of another. For instance, the area of a cube is
related to the length of its sides by a power of 3. A well-known example is the Pareto
distribution or “80/20 rule,” originally used to describe the situation where 20% of a
population controlled 80% of the wealth. We see various power laws in the natural
world and networks.

##### At the most abstract level, graph analytics is applied to forecast behavior and prescribe action for **dynamic groups**. Doing this requires understanding the relationships and structure within the group. Graph algorithms accomplish this by examining the overall nature of networks through their connections. With this approach, you can understand the topology of connected systems and model their processes.

- <img src=../images/graph-question-types.png>

### chapter 2. Graph Theory and Concepts

- The **labeled property** graph is one of the most popular ways of modeling graph data. A *label* marks a node as part of a group. `(:Person)` is a label which determine the group of that particular node.

- <img src=../images/node-rel-types.png>

- A *subgraph* is a graph within a larger graph. Subgraphs are useful as a filters such as
when we need a subset with particular characteristics for focused analysis.

- <img src=../images/network-structure.png>
    - In a completely average distribution of connections, a **random network** is formed
    with no hierarchies. This type of shapeless graph is “flat” with no discernible pat‐
    terns. All nodes have the same probability of being attached to any other node.
    - A **small-world network** is extremely common in social networks; it shows local‐
    ized connections and some *hub-and-spoke pattern*. The “Six Degrees of Kevin
    Bacon” game might be the best-known example of the small-world effect.
    Although you associate mostly with a small group of friends, you’re never many
    hops away from anyone else—even if they are a famous actor or on the other side
    of the planet.
    - A **scale-free network** is produced when there are *power-law distributions* and a
    hub-and-spoke architecture is preserved regardless of scale, such as in the World
    Wide Web.

- Some algorithms struggle with disconnected graphs and can produce misleading
results. If we have unexpected results, checking the structure of our graph is a good first step.

- <img src=../images/graph-att.png>

- 
