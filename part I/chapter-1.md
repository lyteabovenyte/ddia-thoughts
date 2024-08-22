###### Reliable, Scalable, and Maintainable Applications
- A data-intensive application is typically built from standard building blocks that provide commonly needed functionality. For example, many applications need to:
    - Store data so that they, or another application, can find it again later (databases)
    - Remember the result of an expensive operation, to speed up reads (caches)
    - Allow users to search data by keyword or filter it in various ways (search indexes)
    - Send a message to another process, to be handled asynchronously (stream pro‐ cessing)
    - Periodically crunch a large amount of accumulated data (batch processing)

- the boudaries and differences between **kafka** and **Redis**. *worth researching!*

- "if you have an application-managed caching layer (using Memcached or similar), or a full-text search server (such as Elasticsearch or Solr) separate from your main database, it is normally the application code’s responsibility to keep those caches and indexes in sync with the main database" --> think of it 🤓

- combining serveral components:
![combine_serveral_comp](../images/combine_serveral_comp.png)

- questions that arise at the beginning of the implementing a good data system:
    - If you are designing a data system or service, a lot of tricky questions arise. How do you ensure that the data remains correct and complete, even when things go wrong internally? How do you provide consistently good performance to clients, even when parts of your system are degraded? How do you scale to handle an increase in load? What does a good API for the service look like?

- **Reliability**:
    - The system should continue to work correctly (performing the correct function at the desired level of performance) even in the face of adversity (hardware or soft‐ ware faults, and even human error)
- **Scalability**:
    - As the system grows (in data volume, traffic volume, or complexity), there should be reasonable ways of dealing with that growth
- **Maintainability**:
    - Over time, many different people will work on the system (engineering and oper‐ ations, both maintaining current behavior and adapting the system to new use cases), and they should all be able to work on it productively.


- [Orchestrating Data/ML Workflows at Scale With Netflix Maestro](https://netflixtechblog.com/orchestrating-data-ml-workflows-at-scale-with-netflix-maestro-aaa2b41b800c)


- the trade-off between write time and read time can be significantly valuable for designing systems in large scale.
where the decision to make a cache of all the tweets for a users timeline and write them immediately in cache for the followers after posting and decrease the read time on each user's home timeline request and using the cache.


- what is **fan-out load**? --> 
" In the example of Twitter, the distribution of followers per user (maybe weighted by how often those users tweet) is a key load parameter for discussing scalability, since it determines the fan-out load. Your application may have very different characteristics, but you can apply similar principles to reasoning about its load."
  -  The final twist of the Twitter anecdote: now that approach 2 is robustly implemented, Twitter is moving to a hybrid of both approaches. Most users’ tweets continue to be fanned out to home timelines at the time when they are posted, but a small number of users with a very large number of followers (i.e., celebrities) are excepted from this fan-out. Tweets from any celebrities that a user may follow are fetched separately and merged with that user’s home timeline when it is read, like in approach 1. This hybrid approach is able to deliver consistently good performance. We will revisit this example in Chapter 12 after we have covered some more technical ground.

- **throughput**: the number of records we can process per second, or the total time it takes to run a job on a dataset of a certain size


- In a batch processing system such as Hadoop, we usually care about throughput—the number of records we can process per second, or the total time it takes to run a job on a dataset of a certain size. In online systems, what’s usually more important is the service’s response time—that is, the time between a client sending a request and receiving a response

- Usually it is better to use percentiles. If you take your list of response times and sort it from fastest to slowest, then the median is the halfway point: for example, if your median response time is 200 ms, that means half your requests return in less than 200 ms, and half your requests take longer than that.

- 