### Chapter 6
##### Partitioning

- For very large datasets, or very high query throughput, that is not sufficient: we need to break the data up into **partitions**, also known as **sharding**
- In this chapter we will first look at different approaches for partitioning large datasets and observe how the *indexing of data* interacts with partitioning. We’ll then talk about **rebalancing**, which is necessary if you want to add or remove nodes in your cluster. Finally, we’ll get an overview of how databases route requests to the right partitions and execute queries.
- A node may store more than one partition. If a leader–follower replication model is used, Each partition’s leader is assigned to one node, and its followers are assigned to other nodes. Each node may be the leader for some partitions and a follower for other partitions.
<img src=../images/6-1.png>
- The choice of partitioning scheme is mostly independent of the choice of replication scheme

##### partitioning of Key-Value data:
##### Question: Say you have a large amount of data, and you want to partition it. How do you decide which records to store on which nodes?
- If the partitioning is unfair, so that some partitions have more data or queries than others, we call it *skewed*. The presence of skew makes partitioning much less effective. In an extreme case, all the load could end up on one partition, so 9 out of 10 nodes are idle and your bottleneck is the single busy node. A partition with disproportionately high load is called a **hot spot**.
- The simplest approach for avoiding hot spots would be to assign records to nodes randomly. That would distribute the data quite evenly across the nodes, but it has a big disadvantage: when you’re trying to read a particular item, you have no way of knowing which node it is on, so you have to query all nodes in parallel.
- One way of partitioning is to assign a continuous range of keys (from some minimum to some maximum) to each partition, like the volumes of a paper encyclopedia. If you know the boundaries between the ranges, you can easily determine which partition contains a given key. If you also know which partition is assigned to which node, then you can make your request directly to the appropriate node (or, in the case of the encyclopedia, pick the correct book off the shelf).
- 