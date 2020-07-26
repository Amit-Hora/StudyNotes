# Cassandra


- Cassandra uses an approach similar to key-based sharding to distribute data across
nodes, but does so automatically.
- Definition
        - Apache Cassandra is an open source, distributed, decentralized, elastically scalable,
highly available, fault-tolerant, tuneably consistent, row-oriented database. Cassandra
bases its distribution design on Amazon’s Dynamo and its data model on Google’s
Bigtable, with a query language similar to SQL(CQL).
- Cassandra, however, is decentralized, meaning that every node is
identical; no Cassandra node performs certain organizing operations distinct from
any other node. Instead, Cassandra features a peer-to-peer architecture and uses a
gossip protocol to maintain and keep in sync a list of nodes that are alive or dead

- In many distributed data solutions (such as RDBMS clusters), you set up multiple
copies of data on different servers in a process called replication
- You configure your cluster by designating one server as the primary (or primary replica) and others as secondary replicas. The primary replica acts as the authoritative source of the data,
and operates in a unidirectional relationship with the secondary replicas, which must
synchronize their copies. If the primary node fails, the whole database is in jeopardy.

- Decentralization, therefore, has two key advantages: it’s simpler to use than primary/
secondary, and it helps you avoid outages.

- Because Cassandra is distributed and decentralized, there is no single point
of failure, which supports high availability
# Elastic Scalability
- Elastic scalability refers to a special property of horizontal scalability. It means that
your cluster can seamlessly scale up and scale back down.
- Cassandra is Elasticall Scalable
 # High Availability and Fault Tolerance
- Cassandra is highly available. You can replace failed nodes in the cluster with no
downtime, and you can replicate data to multiple data centers to offer improved local
performance and prevent downtime if one data center experiences a catastrophe such
as fire or flood.
# Tuneable Consistency
- Cassandra is frequently called “eventually consistent,” which is a bit misleading. Out of the box, Cassandra trades some consistency in order to achieve total availability. But Cassandra is more accurately
termed “tuneably consistent,” which means it allows you to easily decide the
level of consistency you require, in balance with the level of availability
###  Different Consistency levels
- Strict consistency: This is sometimes called sequential consistency, and is the most stringent level of  consistency. In one single-processor machine, this is no problem to observe, as the sequence of operations is known to the one clock. But in a system executing across a variety of geographically dispersed data centers, it becomes much more slippery. Achieving this implies some
sort of global clock that is capable of timestamping all operations, regardless of
the location of the data or the user requesting it or how many (possibly disparate)
services are required to determine the response
- Causal consistency :This is a slightly weaker form of strict consistency. It does away with the fantasy of the single global clock that can magically synchronize all operations without
creating an unbearable bottleneck. Instead of relying on timestamps, causal consistency
takes a more semantic approach, attempting to determine the cause of
events to create some consistency in their order. It means that writes that are
potentially related must be read in sequence. If two different, unrelated operations
suddenly write to the same field at the same time, then those writes are
inferred not to be causally related. But if one write occurs after another, we might
infer that they are causally related. Causal consistency dictates that causal writes
must be read in sequence
- Weak (eventual) consistency: Eventual consistency means on the surface that all updates will propagate throughout all of the replicas in a distributed system, but that this may take some
time. Eventually, all replicas will be consistent.

When considering consistency, availability, and partition tolerance, we can achieve only two of these goals in a given distributed system, a trade-off known as the CAP theorem. At the center of the problem is data update replication. To achieve a strict consistency, all update operations will be performed synchronously, meaning that they must block, locking all replicas until the operation is complete, and forcing competing clients to wait

We could alternatively take an optimistic approach to replication, propagating updates to all replicas in the background in order to avoid blowing up on the client. The difficulty this approach presents is that now we are forced into the situation of detecting and resolving conflicts. A design approach must decide whether to resolve these conflicts at one of two possible times: during reads or during writes. That is, a distributed database designer must choose to make the system either always readable or always writable.

***
Dynamo and Cassandra choose to be always writable, opting to defer the complexity
of reconciliation to read operations, and realize tremendous performance gains
***
 In Cassandra, consistency is not an all-or-nothing proposition. A more accurate term is “tuneable consistency” because the client can control the number of replicas to block on for all updates. This is done by setting the consistency level against the replication factor
 You set the replication factor to the number of nodes in the cluster you want the updates to propagate to (remember that an update means any add, update, or delete operation).
The consistency level is a setting that clients must specify on every operation and that allows you to decide how many replicas in the cluster must acknowledge a write operation or respond to a read operation in order to be considered successful. That’s the part where Cassandra has pushed the decision for determining consistency out to the client.
So if you like, you could set the consistency level to a number equal to the replication
factor, and gain stronger consistency at the cost of synchronous blocking operations that wait for all nodes to be updated and declare success before returning. This is not often done in practice with Cassandra, however, for reasons that should be clear (it defeats the availability goal, would impact performance, and generally goes against the grain of why you’d want to use Cassandra in the first place). So if the client sets the consistency level to a value less than the replication factor, the update is considered successful even if some nodes are down

# Brewer’s CAP Theorem
- Consistency:All database clients will read the same value for the same query, even given concurrent updates
- Availability: All database clients will always be able to read and write data.
- Partition tolerance: The database can be split into multiple machines; it can continue functioning in the face of network segmentation breaks.

Brewer’s theorem is that in any given system, you can strongly support only two of the three.
![CAP](https://cdn.educba.com/academy/wp-content/uploads/2020/01/CAP-Theorem-last.jpg)

![DB Mapping](https://www.researchgate.net/profile/Joao_Lourenco11/publication/282519669/figure/fig1/AS:281002732736529@1444007680733/CAP-theorem-with-databases-that-choose-CA-CP-and-AP.png)

As Stu Hood points out, a distributed MySQL database can count as a consistent system only if you’re using Google’s synchronous replication patches; otherwise, it can only be available and partition tolerant (AP).

CA
To primarily support consistency and availability means that you’re likely using two-phase commit for distributed transactions. It means that the system will block when a network partition occurs, so it may be that your system is limited to a single data center cluster in an attempt to mitigate this. If your application needs only this level of scale, this is easy to manage and allows you to rely on familiar, simple structures.
CP
To primarily support consistency and partition tolerance, you may try to advance your architecture by setting up data shards in order to scale. Your data will be consistent, but you still run the risk of some data becoming unavailable if nodes fail.
AP
To primarily support availability and partition tolerance, your system may return inaccurate data, but the system will always be available, even in the face of network partitioning. DNS is perhaps the most popular example of a system that is massively scalable, highly available, and partition tolerant.
