# Chapter 4 - Scaling Databases - (Q&A)

1. What is the primary difference between stateful and stateless services? #stateful-services #stateless-services

Answer: The primary difference between stateful and stateless services is that stateful services have mechanisms to ensure consistency and require redundancy to avoid data loss, while stateless services do not maintain any state or persistent data. Stateful services, such as storage services, employ techniques like strong consistency (e.g., Paxos) or eventual consistency mechanisms to manage state and handle data consistency. In contrast, stateless services do not have to worry about managing state or ensuring data consistency across multiple requests.

2. Explain the concept of strong consistency and weak (eventual) consistency. #strong-consistency #weak-consistency

Answer: Strong consistency ensures that all parallel processes (or nodes, processors, etc.) see accesses in the same order (sequentially), leading to a single, consistent state observable across the system. In contrast, weak or eventual consistency allows different parallel processes to perceive variables in different states temporarily. In other words, strong consistency provides a linear, consistent view of data across the system, while eventual consistency allows for temporary inconsistencies before converging to a consistent state.

3. What are the reasons for keeping state only in stateful services? #sticky-sessions #failover #replication

Answer: There are several reasons for keeping state only in stateful services:

- It avoids the need for sticky sessions, which consistently route the same user to the same host, simplifying the architecture.
- It eliminates the requirement to replicate data and handle failover mechanisms on individual hosts, reducing complexity.
- It allows choosing the appropriate storage/database technology for specific requirements, rather than designing and implementing state management within individual services, reducing the risk of errors.

4. Differentiate between the following types of storage: SQL, NoSQL, column-oriented, key-value, document, graph, file storage, and block storage. #database #sql #nosql #column-oriented #key-value #document #graph #file-storage #block-storage

- **SQL** databases are relational databases with tables, relationships, primary keys, and foreign keys. They must have ACID properties (Atomicity, Consistency, Isolation, Durability).
- **NoSQL** databases do not have all the properties of SQL databases and may sacrifice some ACID properties for scalability and performance.
- **Column-oriented** databases organize data into columns instead of rows, optimizing for efficient filtering. Examples include Cassandra and HBase.
- **Key-value** databases store data as a collection of key-value pairs, where keys are hashable (primitive types) and values can be primitives or pointers. They are often used for caching (e.g., Memcached, Redis).
- **Document** databases can be seen as a variant of key-value databases, but the values have no size limits or much larger limits, and can be in formats like JSON or YAML (e.g., MongoDB).
- **Graph** databases are designed to efficiently store and query relationships between entities (e.g., Neo4j, RedisGraph, Amazon Neptune).
- **File storage** involves storing data in files organized into directories/folders, similar to a key-value store with paths as keys.
- **Block storage** stores data in evenly sized chunks with unique identifiers and is typically used in low-level storage systems rather than web applications.

5. What is replication, and how does it enable fault-tolerance, scalability, and low latency? #replication #fault-tolerance #scalability #latency

Answer: Replication is the process of making copies of data, called replicas, and storing them on different nodes. It enables several benefits:

- **Fault-tolerance**: Replicas can back up data on other nodes within and across data centers, allowing failover processes to take over roles and partitions/shards of failed nodes.
- **Higher throughput**: Replication distributes the load of processing reads and writes across multiple nodes, increasing overall throughput.
- **Lower latency**: Replicas can be geographically distributed closer to dispersed users, reducing latency for local requests.

6. What is the typical design for distributing replicas across racks and data centers? #replica-distribution #rack-awareness #data-center-awareness

Answer: The typical design for distributing replicas involves:

- Having one replica on a host within the same rack as the primary node.
- Having another replica on a host in a different rack or data center (or both) from the primary node.

This design ensures redundancy and fault-tolerance across racks and data centers, reducing the risk of data loss due to rack or data center failures.

7. Explain the concept of single-leader replication and its advantages and limitations. #single-leader #read-scalability #write-bottleneck

Answer: In single-leader replication, all write operations occur on a single node called the leader, and these writes are then replicated to follower nodes. The main advantages of single-leader replication are:

- It scales read operations by allowing the creation of multiple read replicas.
- It is relatively simple to implement.

However, single-leader replication has some limitations:

- Write operations are still bottlenecked on the leader node, limiting write scalability.
- In SQL databases, single-leader replication loses ACID consistency on the follower nodes, as replication to followers takes time (eventual consistency).
- The entire database must fit into a single host (the leader node).

8. What is multi-level replication, and how does it help scale read operations further? #multi-level-replication #consistency-delay

Answer: Multi-level replication is a technique used in single-leader replication to scale read operations even further. It involves creating multiple levels of follower nodes, arranged in a pyramid-like structure. Each node replicates to a number of followers in the level below, and those followers further replicate to their respective followers, and so on.

The advantage of multi-level replication is that each node can replicate to the number of followers it is capable of handling, allowing for more read replicas overall. However, the tradeoff is that consistency is further delayed as data propagates through the levels, with the nodes at the bottom levels having the most eventual consistency.

9. What are the challenges introduced by multi-leader replication, and how are they addressed? #multi-leader #write-scalability #race-conditions #consistency-problems #clock-skew #sequence-importance

Answer: Multi-leader replication, where writes can occur on any leader node, introduces several challenges:

- **Race conditions**: When concurrent writes or updates occur on different leader nodes, race conditions can arise, leading to inconsistencies if the operations are not properly ordered and handled.
- **Consistency problems**: Ensuring consistency becomes more complex, as the sequence of operations is important for certain operations like UPDATE and DELETE. Using timestamps alone does not work due to the clock skew problem (imperfect clock synchronization across nodes).
- **Sequence importance**: For some operations, the sequence in which they are executed matters. If different replicas process the same operations in different orders, inconsistencies can arise.

10. What is the concept of quorum in leaderless replication, and how does it help maintain consistency? #leaderless #quorum #consensus

Answer: In leaderless replication, where all nodes are equal and reads/writes can occur on any node, the concept of quorum is used to maintain consistency. A quorum is the minimum number of nodes that must agree or acknowledge an operation for it to be considered successful and consistent.

By setting appropriate read and write quorums, leaderless replication systems can balance consistency and availability. For example, if there are N nodes, setting both read and write quorums to N/2 + 1 ensures that any read or write operation involves a majority of nodes, providing strong consistency.

# Section 4.4 to 4.7

1. What are the options for handling data when the database size exceeds a single host's capacity? #database-scaling #single-host-capacity
   Answer: The two main options are: a) Delete old rows to make space, or b) Use sharded storage solutions like HDFS or Cassandra, which can scale horizontally by adding more hosts to support larger storage capacities. #delete-old-data #sharded-storage #horizontal-scaling

2. Why should we aim to reduce the rate of database writes in our system's design? #reduce-database-writes
   Answer: Database writes are difficult and expensive to scale. Reducing the write rate through techniques like sampling and aggregation not only reduces the load on the database but also slows down the growth of the database size. #sampling #aggregation #slower-database-growth

3. What is event aggregation, and how can it be implemented? #event-aggregation #implementation
   Answer: Event aggregation combines multiple events into a single event, reducing the number of database writes. It can be implemented using a streaming pipeline with multiple stages, where each subsequent stage has fewer hosts, reducing resource requirements. Replication and checkpointing ensure fault tolerance and data accuracy. #reduced-writes #streaming-pipeline #multi-stage #replication #checkpointing

4. How can partitioning be used in event aggregation, and what are its benefits? #partitioning #benefits
   Answer: Partitioning involves configuring the load balancer to forward events to specific hosts based on their contents. This allows allocating different numbers of hosts to each partition based on expected traffic distribution. Partitions can also be adjusted dynamically to balance traffic across them. #load-balancer-configuration #uneven-traffic #resource-allocation #dynamic-partitioning

5. What is the difference between batch and streaming processing in the context of ETL? #batch-processing #streaming-processing #etl
   Answer: Batch processing refers to processing data in periodic batches, regardless of new events, similar to polling. Streaming processing refers to a continuous flow of data being processed in real-time, similar to interrupt-driven processing when new events occur. #periodic-batches #polling #continuous-flow #real-time #interrupt-driven

6. What is an ETL pipeline, and what are its components? #etl-pipeline #components
   Answer: An ETL (Extract, Transform, Load) pipeline consists of a Directed Acyclic Graph (DAG) of tasks, where nodes represent tasks, and ancestors are dependencies. A single run of the pipeline is called a job. The tasks are responsible for extracting data from sources, transforming it, and loading it into the destination system. #dag #tasks #dependencies #job

7. How can a simple batch ETL pipeline be horizontally scaled for better scalability and availability? #horizontal-scaling #batch-etl
   Answer: Dedicated job scheduling systems like Airflow and Luigi can be used for horizontal scaling. They provide web UIs, DAG visualization, cluster support, and better handling of parallelism and high availability compared to a simple crontab-based approach. #job-scheduling-systems #web-ui #dag-visualization #cluster-support #parallelism #high-availability

8. Explain the key differences between Kafka and RabbitMQ. #kafka #rabbitmq #comparison
   Answer: Kafka is more complex than RabbitMQ but provides a superset of capabilities. It is designed for scalability, reliability, and availability, with durable message storage and replication. RabbitMQ is simpler to set up but not as scalable or durable by default. Kafka allows repeated consumption of events for failure tolerance, while RabbitMQ follows traditional queue behavior. #complexity #superset-capabilities #scalability #reliability #availability #durability #replication #failure-tolerance #queue-behavior

9. What is Lambda architecture, and when is it useful? #lambda-architecture #use-case
   Answer: Lambda architecture is a data-processing architecture for big data, running batch and streaming pipelines in parallel. The fast pipeline trades off consistency and accuracy for lower latency, while the slow pipeline prioritizes consistency and accuracy over low latency. It is useful for systems involving big data that require both consistency/accuracy and low latency. #big-data #fast-pipeline #consistency #accuracy #low-latency #slow-pipeline

10. **#NormalizationBenefits** What are the benefits of normalization when data fits in a single host?

    - Consistent data with no duplicates
    - Faster inserts and updates (query only one table)
    - Smaller database size and faster reads (no duplicate data)
    - Fewer indexes, faster index rebuilds
    - Join only required tables in queries

11. **#NormalizationDisadvantages** Disadvantages of normalization?

    - Complex queries requiring multiple table joins
    - Increased memory usage for queries

12. **#CachingBenefits** Benefits of caching?

    - Improved performance (memory faster than disk)
    - Increased availability (serve cached data if database unavailable)
    - Enhanced scalability (serve frequently requested data from cache)

13. **#ReadStrategies** Difference between cache-aside and read-through?

    - Cache-aside: Read from cache first, on miss read from database and write to cache
    - Read-through: Application requests cache, cache handles database requests if needed
    - Cache-aside best for read-heavy loads, read-through cannot group multiple database requests

14. **#WriteStrategies** Differences between write strategies?

    - Write-through: Every write goes to cache and database (consistent but slower)
    - Write-back/behind: Application writes to cache, cache periodically flushes to database (faster)
    - Write-around: Application only writes to database, cache updated on miss

15. **#SeparateCachingService** Why caching as a separate service?

    - Services are stateless, requests randomly assigned (reducing cache hits on hosts)
    - Caching effective for uneven request patterns and hot shards
    - Avoid cache wipes during frequent deployments on hosts
    - Independent scaling and optimized hardware/VMs for caching
    - Request coalescing (deduplicate requests to service)

16. **#DataNotToBeCached** What information should not be cached?

    - Private information (e.g., bank account details)
    - Realtime public information (e.g., stock prices, flight times)
    - Paid or copyrighted content (e.g., books, videos)

17. **#CacheInvalidation** Client-side cache invalidation techniques?

    - Setting max-age value
    - Fingerprinting files with hashes or query parameters

18. **#CacheReplacement** Common cache replacement policies in caching services?

    - Random replacement
    - Least Recently Used (LRU)
    - First In First Out (FIFO)
    - Last In First Out (LIFO)

19. **#CacheWarming** Cache warming pros and cons?
    - Pros: First request has low latency like subsequent requests
    - Cons: Complexity, additional traffic, service load, slow first user, long expiry times, cache size
