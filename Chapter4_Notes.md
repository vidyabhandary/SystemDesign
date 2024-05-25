# Chapter 4 - Scaling Databases (Summary)

# Storage Services #stateful-services #consistency #redundancy

- Stateful services like storage have mechanisms for consistency and redundancy to avoid data loss
- They use consensus protocols like Paxos for strong consistency or eventual consistency mechanisms
- Tradeoffs involve consistency, complexity, security, latency, performance
- Stateless services are preferred, state is kept only in stateful services

**Key Point:** _Understand the tradeoffs involved in choosing consistency mechanisms for stateful services._

## Strong vs Weak Consistency #strong-consistency #weak-consistency

- In strong consistency, all parallel processes see accesses in the same order (sequentially)
- Only one consistent state is observed across processes
- In weak/eventual consistency, different processes may see variables in different states

**Key Point:** _Strong consistency provides a single linear view, while eventual consistency allows temporary inconsistencies._

# Types of Storage #database #sql #nosql #column-oriented #key-value #document #graph #file-storage #block-storage

- **SQL:** Relational, with tables, keys; must have ACID properties
- **NoSQL:** Does not have all SQL properties
- **Column-oriented:** Data organized into columns (e.g. Cassandra, HBase)
- **Key-value:** Data as key-value pairs, keys are hashable (e.g. Memcached, Redis)
- **Document:** Key-value with larger values in formats like JSON, YAML (e.g. MongoDB)
- **Graph:** Designed for efficient relationship storage (e.g. Neo4j, RedisGraph)
- **File Storage:** Data stored in files/directories, like key-value
- **Block Storage:** Data in fixed-size blocks, used in low-level storage systems

**Key Point:** _Understand the different types of storage and their characteristics to choose the appropriate one for your requirements._

# Replication #replication #partitioning #sharding #fault-tolerance #scalability #latency

- Replication makes copies (replicas) of data on different nodes
- Partitioning divides data into subsets, sharding distributes partitions across nodes
- Enables fault-tolerance, higher storage capacity, throughput, and lower latency

## Replica Distribution #replica-distribution #rack-awareness #data-center-awareness

- Typical design: one replica on same rack, one on different rack/data center
- Sharding provides storage scaling, memory scaling, parallel processing, and data locality

**Key Point:** _Replication and sharding are key techniques for scalability, fault-tolerance, and performance in distributed systems._

## Single-Leader Replication #single-leader #read-scalability #write-bottleneck

- All writes occur on a single leader node, replicated to followers
- Scales reads by increasing replicas, but writes are still bottlenecked
- Examples: MySQL, Postgres replication configurations

### Multi-Level Replication #multi-level-replication #consistency-delay

- Multiple levels of followers, like a pyramid, to scale reads further
- Each node replicates to followers it can handle
- Tradeoff: consistency is further delayed down the levels

**Key Point:** _Single-leader replication is simple but has limitations in write scalability and consistency._

## Multi-Leader Replication #multi-leader #write-scalability #race-conditions

- Multiple leader nodes, writes can occur on any leader
- Each leader replicates writes to all other nodes
- Introduces race conditions and consistency problems

### Consistency Problems #consistency-problems #clock-skew #sequence-importance

- Sequence is important for operations like UPDATE and DELETE
- Using timestamps doesn't work due to clock skew (imperfect clock sync)
- Different replicas may process the same operations in different orders

**Key Point:** _Multi-leader replication enables write scalability but requires handling race conditions and consistency issues._

## Leaderless Replication #leaderless #quorum #consensus

- All nodes are equal, reads and writes can occur on any node
- Introduces race conditions, handled using the concept of quorum
- Quorum is the minimum number of nodes required for consensus
- Examples: Cassandra, Dynamo, Riak, Voldemort

### Quorum Configurations #quorum-configurations #write-quorum #read-quorum

- For n nodes, read and write quorums can be set to n/2 + 1 for consistency
- Otherwise, only eventual consistency, and UPDATE/DELETE may be inconsistent

**Key Point:** _Leaderless replication requires careful quorum configuration to balance consistency and performance._

# Key Takeaways

1. **Consistency vs. Availability Tradeoff:** Understand the tradeoffs involved in choosing consistency mechanisms (strong, eventual) and their impact on availability and performance.

2. **Replication Strategies:** Grasp the different replication strategies (single-leader, multi-leader, leaderless) and their advantages, limitations, and consistency implications.

3. **Storage Types:** Familiarize yourself with the different types of storage (SQL, NoSQL, column-oriented, key-value, document, graph, file, block) and their suitable use cases.

4. **Scalability Techniques:** Leverage techniques like replication, partitioning, and sharding to achieve fault-tolerance, increased storage capacity, throughput, and lower latency.

5. **Distributed Systems Complexities:** Be aware of complexities in distributed systems, such as race conditions, clock skew, and the need for consensus algorithms and quorum configurations.

6. **Further Reading:** Refer to resources like "Designing Data-Intensive Applications" by Martin Kleppmann for more in-depth coverage of topics like consensus algorithms, failover problems, and multi-leader replication.

References:

1. [Partitioning Vs Sharding](https://github.com/vidyabhandary/TIL/blob/501537d24742b8730b879e10ea22d009ba950ed4/misc/PartioningVsSharding.md)
