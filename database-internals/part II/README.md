
### <span style="color: #f2cf4a; font-family: Babas; font-size: 3em;">Database internals</span>

#### <span style="color: #f2cf4a; font-family: Babas; font-size: 2em;">Part II</span>

#### <span style="color: #f2cf4a; font-family: Babas; font-size: 2em;">Distributed systems</span>

"A distributed system is one in which the failure of a computer you didn’t even know existed can render your own computer unusable." -Leslie Lamport

Without distributed systems, we wouldn’t be able to make phone calls, transfer money, or exchange information over long distances. We use distributed systems daily. Sometimes, even without acknowledging it: any client/server application is a distributed system.
##### Part II. Basic definitions:

In a distributed system, we have several participants (sometimes called processes, nodes, or replicas). Each participant has its own local state. Participants communicate by exchanging messages using communication links between them.


Processes can access the time using a clock, which can be logical or physical. Logical clocks are implemented using a kind of monotonically growing counter. Physical clocks, also called wall clocks, are bound to a notion of time in the physical world and are accessible through process-local means; for example, through an operating system.


Most of the research in the distributed systems field is related to the fact that nothing is entirely reliable: communication channels may delay, reorder, or fail to deliver the messages; processes may pause, slow down, crash, go out of control, or suddenly stop responding.



There are many themes in common in the fields of concurrent and distributed programming, since CPUs are tiny distributed systems with links, processors, and communication protocols. You’ll see many parallels with concurrent programming in “Consistency Models”. However, most of the primitives can’t be reused directly because of the costs of communication between remote parties, and the unreliability of links and processes.



To overcome the difficulties of the distributed environment, we need to use a particular class of algorithms, **distributed algorithms**, which have notions of local and remote state and execution and work despite unreliable networks and component failures. We describe algorithms in terms of state and steps (or phases), with transitions between them. Each process executes the algorithm steps locally, and a combination of local executions and process interactions constitutes a distributed algorithm.



Distributed algorithms describe the local behavior and interaction of multiple independent nodes. Nodes communicate by sending messages to each other.


Algorithms define participant roles, exchanged messages, states, transitions, executed steps, properties of the delivery medium, timing assumptions, failure models, and other characteristics that describe processes and their interactions.


Distributed algorithms serve many different purposes:
- **Coordination**: A process that supervises the actions and behavior of several workers
- **Cooperation**: Multiple participants relying on one another for finishing their tasks.
- **Dissemination**: Processes cooperating in spreading the information to all interested parties quickly and reliably.
- **Consensus**: Achieving agreement among multiple processes.

