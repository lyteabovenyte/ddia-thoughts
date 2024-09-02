#### <span style="color: #f2cf4a; font-family: Babas; font-size: 3em;">Database internals</span>

#####  <span style="color: #f2cf4a; font-family: Babas; font-size: 2em;">Chapter 8</span>


##### Concurrency:
- Even before we can cross a single node boundary, we encounter the first problem in distributed systems: **concurrency**. Every concurrent program has some properties of a distributed system. Threads access the shared state, perform some operations locally, and propagate the results back to the shared variables.
- To define execution histories precisely and reduce the number of possible outcomes, we need **consistency models**. Consistency models describe concurrent executions and establish an order in which operations can be executed and made visible to the participants.
- There is a lot of overlap in terminology and research in the areas of distributed systems and concurrent computing, but there are also some differences. In a concurrent system, we can have shared memory, which processors can use to exchange the information. In a distributed system, each processor has its local state and participants communicate by passing messages.
- differences between concurrent and parallel:
  - We often use the terms concurrent and parallel computing interchangeably, but these concepts have a slight semantic difference. When two sequences of steps execute concurrently, both of them are in progress, but only one of them is executed at any moment. If two sequences execute in parallel, their steps can be executed simultaneously. Concurrent operations overlap in time, while parallel operations are executed by multiple processors

##### Shared State in a Distributed System
We can try to introduce some notion of shared memory to a distributed system, for example, a single source of information, such as *database*. Even if we solve the problems with concurrent access to it, we still cannot guarantee that all processes are in *sync*.
To access this database, processes have to go over the communication medium by sending and receiving messages to query or modify the state. However, what happens if one of the processes does not receive a response from the database for a longer time? To answer this question, we first have to define what '*longer*' even means. To do this, the system has to be described in terms of **synchrony**: whether the communication is fully asynchronous, or whether there are some timing assumptions. These timing assumptions allow us to introduce operation timeouts and retries.

A property that describes system reliability and whether or not it can continue operating correctly in the presence of failures is called fault tolerance.

sharing state is not as simple as just introducing a database, and have to take a more granular approach and describe interactions in terms of independent processes and passing messages between them.

##### Fallacies of Distributed Computing
Most of the time, assuming that the network is reliable is a reasonable thing to do. It has to be reliable to at least some extent to be useful. We’ve all been in the situation when we tried to establish a connection to the remote server and got a Network is Unreachable error instead. But even if it is possible to establish a connection, a successful initial connection to the server does not guarantee that
the link is stable, and the connection can get interrupted at any time. The message might’ve reached the remote party, but the response could’ve gotten lost, or the connection was interrupted before the response was delivered.


Network switches break, cables get disconnected, and network configurations can change at any time. We should build our system by handling all of these scenarios gracefully.
A connection can be stable, but we can’t expect remote calls to be as fast as the local ones. We should make as few assumptions about latency as possible and never assume that latency is zero. For our message to reach a remote server, it has to go through several software layers, and a physical medium such as optic fiber or a cable. All of these operations are not instantaneous.

#### Processing

Before a remote process can send a response to the message it just received, it needs to perform some work locally, so we cannot assume that processing is *instantaneous*. Taking network latency into consideration is not enough, as operations performed by the remote processes aren’t immediate, either.

- Decoupling: Receipt and processing are separated in time and happen independently.
- Pipelining: Requests in different stages are processed by independent parts of the system. The subsystem responsible for receiving messages doesn’t have to block until the previous message is fully processed.
- Absorbing short-time bursts: System load tends to vary, but request inter-arrival times are hidden from the component responsible for request processing. Overall system latency increases because of the time spent in the queue, but this is usually still better than responding with a failure and retrying the request.


Queue size is workload- and application-specific. For relatively stable workloads, we can size queues by measuring task processing times and the average time each task spends in the queue before it is processed, and making sure that latency remains within acceptable bounds while throughput increases. In this case, queue sizes are relatively small. For unpredictable workloads, when tasks get submitted in bursts, queues should be sized to account for bursts and high load as well.

#### Clocks as Time

any synchronous system uses *local clocks* for timeouts.

It’s essential to always account for the possible time differences between the processes and the time required for the messages to get delivered and processed.


Besides the fact that clock synchronization in a distributed system is hard, the current time is constantly changing: you can request a current POSIX timestamp from the operating system, and request another current timestamp after executing several steps, and the two will be different. This is a rather obvious observation, but understanding both a source of time and which exact moment the timestamp captures is crucial.

#### State Consistency

Distributed algorithms do not always guarantee strict state consistency. Some approaches have looser constraints and allow state divergence between replicas, and rely on <u>conflict resolution</u> (an ability to detect and resolve diverged states within the system) and <u>read-time data repair</u> (bringing replicas back in sync
during reads in cases where they respond with different results). Assuming that the state is fully consistent across the nodes may lead to subtle bugs.

An eventually consistent distributed database system might have the logic to handle replica disagreement by querying a quorum of nodes during reads, <u>but assume that the database schema and the view of the cluster are strongly consistent.</u>Unless we enforce consistency of this information, relying on that assumption may have severe consequences.

#### Local and Remote execution

Hiding complexity behind an API might be dangerous. For example, if you have an iterator over the local dataset, you can reasonably predict what’s going on behind the scenes, even if the storage engine is unfamiliar. Understanding the process of iteration over the remote dataset is an entirely different problem: you need to understand consistency and delivery semantics, data reconciliation, paging, merges, concurrent access implications, and many other things. Simply hiding both behind the same interface, however useful, might be misleading. Additional API parameters may be necessary for debugging, configuration, and observability

#### Need to Handle Failures

If the remote server doesn’t respond, we do not always know the exact reason for it. It could be caused by the crash, a network failure, the remote process, or the link to it being slow. Some distributed algorithms use heartbeat protocols and failure detectors to form a hypothesis about which participants are alive and reachable.

#### Network Partitions and Partial Failures

When two or more servers cannot communicate with each other, we call the situation **network partition**. 

To build a system that is robust in the presence of failure of one or multiple processes, we have to consider cases of partial failures and how the system can continue operating even though a part of it is unavailable or functioning incorrectly.

The best way to design for failures is to test for them. It’s close to impossible to think through every possible failure scenario and predict the behaviors of multiple processes. Setting up testing harnesses that create partitions, simulate bit rot, increase latencies, diverge clocks, and magnify relative processing speeds is the best way to go about it.

 #### Cascading Failures

We cannot always wholly isolate failures: a process tipping over under a high load increases the load for the rest of cluster, making it even more probable for the other nodes to fail. Cascading failures can propagate from one part of the system to the other, increasing the scope of the problem.

Sometimes, cascading failures can even be initiated by perfectly good intentions. For example, a node was offline for a while and did not receive the most recent updates. After it comes back online, helpful peers would like to help it to catch up with recent happenings and start streaming the data it’s missing over to it, exhausting network resources or causing the node to fail shortly after the startup.

To protect a system from propagating failures and treat failure scenarios gracefully, circuit breakers can be used. In electrical engineering, circuit breakers protect expensive and hard-to- replace parts from overload or short circuit by interrupting the current flow. In software development, circuit breakers monitor failures and allow fallback mechanisms that can protect the system by steering away from the failing service, giving it some time to recover, and handling failing calls gracefully.

When the connection to one of the servers fails or the server does not respond, the client starts a reconnection loop. By that point, an overloaded server already has a hard time catching up with new connection requests, and client-side retries in a tight loop don’t help the situation. To avoid that, we can use a **backoff strategy**. Instead of retrying immediately, clients wait for some time. Backoff can help us to avoid amplifying problems by scheduling retries and increasing the time window between subsequent requests.

Backoff is used to increase time periods between requests from a single client. However, different clients using the same backoff strategy can produce substantial load as well. To prevent different clients from retrying all at once after the backoff period, we can introduce **jitter**. Jitter adds small random time periods to backoff and reduces the probability of clients waking up and retrying at the same time.

