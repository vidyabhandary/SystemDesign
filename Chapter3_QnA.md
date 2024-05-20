# Chapter 3 (Q&A) - Non-Functional Requirements

Questions and Answers:

1. What are functional requirements, and how are they typically represented? #functional-requirements
   A: Functional requirements describe the inputs and outputs of a system. They can be represented as a rough API specification and endpoints. For example, an API for retrieving a list of nearby restaurants would be a functional requirement.

2. Define non-functional requirements and list some examples. #non-functional-requirements
   A: Non-functional requirements refer to requirements other than the system's inputs and outputs. Examples include scalability, availability, performance/latency/throughput, fault tolerance, security, privacy, accuracy, consistency, cost, complexity, maintainability, debuggability, and testability.

3. Why is it important to clarify requirements, both functional and non-functional? #clarifying-requirements
   A: It is essential to clarify requirements because customers may not explicitly request non-functional requirements, and their stated requirements may be incomplete, incorrect, or excessive. Without clarification, there will be misunderstandings, and the system may inadequately satisfy the requirements or provide an excessive solution.

4. Explain the concept of trade-offs in the context of non-functional requirements. #trade-offs
   A: Non-functional requirements are commonly traded off against each other. In system design, various design decisions must be made to balance trade-offs among different non-functional requirements. For example, a design decision that improves scalability may compromise availability or increase complexity.

5. What is the difference between vertical scaling and horizontal scaling? #vertical-scaling #horizontal-scaling
   A: Vertical scaling involves upgrading to a more powerful and expensive host, while horizontal scaling refers to spreading out the processing and storage requirements across multiple hosts. True scalability can only be achieved through horizontal scaling, as vertical scaling has technological limits and potential downtimes during upgrades.

6. Explain the purpose and types of load balancing. #load-balancing
   A: Load balancing distributes traffic across multiple hosts in a system. It can be achieved through hardware load balancers, shared load balancer services (LBaaS), or software load balancers like HAProxy and NGINX. Load balancers can operate at the transport layer (level 4) or the application layer (level 7), with different capabilities in terms of routing decisions, authentication, and TLS termination.

7. What are sticky sessions, and when are they used? #sticky-sessions
   A: Sticky sessions refer to a load balancer sending requests from a particular client to a specific host for a duration. They are used for stateful services, such as e-commerce websites, social media platforms, or banking applications, where user session data like login information, preferences, or shopping carts need to be maintained across requests.

8. Describe the concept of session replication and its benefits. #session-replication
   A: Session replication involves replicating writes to a host across several other hosts assigned to the same session. This allows reads to be routed to any host with that session, improving availability. For example, if there are three hosts in a session, when host A receives a write, it writes to host B, which in turn writes to host C. This ensures that data is replicated across multiple hosts for the same session, providing redundancy and fault tolerance.

9. What is the difference between load balancing and reverse proxy? #load-balancing #reverse-proxy
   A: Load balancing is primarily focused on scalability, distributing traffic across multiple hosts. On the other hand, a reverse proxy acts as a gateway between clients and servers, managing client-server communication. It can perform tasks such as caching, compression, and SSL termination, in addition to forwarding requests to the appropriate server based on criteria like the request URI.

10. Define availability and explain its importance in system design. #availability
    A: Availability is the percentage of time a system can accept requests and return the desired response. High availability is often a critical requirement in system design, as it ensures that the system is accessible and functioning for users or other dependent systems. Techniques like replication, circuit breakers, fallback patterns, and asynchronous communication can be employed to improve availability.

11. Why is it important to clarify availability requirements, and what factors should be considered? #clarifying-availability
    A: It is crucial to clarify availability requirements because high availability may not always be necessary, and it can be traded off against other non-functional requirements like consistency and latency (as per the CAP theorem). Factors to consider include whether immediate responses are required for all requests or if acknowledgments and delayed responses are acceptable. This can simplify the system design and enable the use of asynchronous communication techniques.

12. What are incident metrics, and why are they important in the context of availability? #incident-metrics
    A: Incident metrics, such as Mean Time to Recovery (MTTR) and Mean Time Between Failures (MTBF), are used to measure and monitor the availability of a system. They provide insights into the system's downtime, recovery time, and frequency of failures. These metrics are important for setting up dashboards and alerts, enabling proactive monitoring and timely response to availability issues.

13. Define fault tolerance and its importance in system design. #fault-tolerance
    A: Fault tolerance is the ability of a system to continue operating if some of its components fail, preventing data loss or downtime. It allows for graceful degradation, where the system can maintain partial functionality even when parts of it fail. Fault tolerance is crucial in system design to ensure reliability and prevent complete system failures, buying time for engineers to fix the failed sections and restore the system to working order.

14. Explain the concept of replication and redundancy in the context of fault tolerance. #replication #redundancy
    A: Replication and redundancy are techniques used to achieve fault tolerance. Replication involves creating multiple instances or copies of a component, typically with a leader-follower or multi-master replication model. Redundancy refers to having multiple redundant instances or copies of a component, so that up to a certain number can be simultaneously down without affecting uptime. By introducing redundancy through replication, the system can continue operating even if some components fail.

15. What is a circuit breaker, and how does it contribute to fault tolerance? #circuit-breaker
    A: A circuit breaker is a mechanism that stops a client from repeatedly attempting an operation that is likely to fail. It calculates the number of failed requests within a recent interval and, if an error threshold is exceeded, stops calling downstream services for a period of time. This prevents the client from adding additional burden to an already overburdened system and saves resources from being spent on requests that are likely to fail, contributing to fault tolerance.

16. Describe the fallback pattern and its role in fault tolerance. #fallback-pattern
    A: The fallback pattern involves detecting a problem and executing an alternative code path, such as using cached responses or alternative services that are similar to the service the client is trying to get information from. It provides graceful degradation by allowing the system to continue functioning, albeit with potentially stale or less accurate data, when components fail. The fallback pattern is a crucial aspect of fault tolerance, ensuring that the system does not completely fail in the event of component failures.

17. What is checkpointing, and how does it help in fault tolerance? #checkpointing
    A: Checkpointing is a technique used in data processing operations where progress markers (checkpoints) are written after each subset of data points is processed and the result is successfully written. If a machine fails during data aggregation, the replacement machine can resume processing from the most recent checkpoint instead of starting from the beginning. This ensures that data is not lost and the operation can continue from the last known good state, contributing to fault tolerance.

18. Explain the concept of a dead letter queue and its use in fault tolerance. #dead-letter-queue
    A: A dead letter queue is a queue where failed write requests to a third-party API or service are temporarily stored. This allows the system to retry these requests later instead of immediately failing or losing data. A dead letter queue can be implemented locally within the system or using an event streaming platform like Kafka. It contributes to fault tolerance by providing a mechanism to handle and recover from failed write operations, preventing permanent data loss or system failures.

19. How can logging and periodic auditing help in achieving fault tolerance? #logging #auditing
    A: Logging write requests and periodically auditing the data stored on the target service can help detect and mitigate silent or undetected errors. By comparing the logged write requests with the actual data stored, discrepancies can be identified and addressed. This contributes to fault tolerance by ensuring data integrity and providing a mechanism to recover from silent errors that may otherwise go unnoticed and potentially lead to system failures or data loss.

20. What is the bulkhead pattern, and how does it contribute to fault tolerance? #bulkhead-pattern
    A: The bulkhead pattern is a fault tolerance mechanism where a system is divided into isolated pools or compartments, so that a fault in one pool does not affect the entire system. It involves isolating system components, such as service endpoints or hosts, into separate pools, preventing failures in one pool from cascading and causing a complete system outage.

21. Define performance, latency, and throughput in the context of system design. #performance #latency #throughput
    A: Performance or latency refers to the time taken for a user's request to the system to receive a response. This includes network latency and the time the system takes to process the request and generate the response. Throughput is the current request rate being processed by the system. Low latency corresponds to high throughput, and vice versa. Ensuring low latency and high throughput is often a crucial non-functional requirement in system design.

22. What techniques can be used to reduce latency in a system? #reducing-latency
    A: Techniques to reduce latency include deploying the service closer to users or in multiple data centers to minimize geographical distance, using Content Delivery Networks (CDNs), caching, batch and streaming techniques, and minimizing message sizes through techniques like RPC serialization frameworks and metadata services.

23. Why is it important to clarify latency requirements, and what factors should be considered? #clarifying-latency
    A: It is essential to clarify latency requirements because the system's functional requirements may not necessitate immediate responses containing the requested information. Factors to consider include whether acknowledgments or delayed responses are acceptable, and whether certain operations can be processed asynchronously using batch or streaming techniques. Clarifying these requirements can simplify the system design and enable the use of appropriate techniques.

24. What is the difference between ACID consistency and CAP consistency (linearizability)? #acid #cap #consistency
    A: ACID consistency focuses on data relationships like foreign keys and uniqueness within a single database or transaction. CAP consistency, also known as linearizability, refers to all nodes in a distributed system containing the same data at a moment in time, and changes in data being served by all nodes simultaneously. It is a broader concept related to data consistency across multiple nodes or replicas in a distributed system.

25. Define eventual consistency and explain its trade-offs. #eventual-consistency
    A: Eventual consistency is a approach where systems trade off strict consistency (linearizability) for improvements in availability, scalability, and latency. In an eventually consistent system, reads may not immediately reflect the effects of recent writes, as data is eventually propagated and replicated across different nodes or replicas. The trade-off is that eventual consistency provides higher availability, scalability, and lower latency compared to linearizable systems, but at the cost of temporary inconsistencies or stale data until replication is complete.

26. What is a full mesh, and why is it not considered scalable? #full-mesh
    A: A full mesh is a technique for maintaining consistency in a distributed system, where every host in the cluster has the address of every other host and broadcasts messages to all of them. While full mesh is easier to implement, it is not scalable because the number of messages grows quadratically with the number of hosts, making it suitable only for small clusters.

27. Explain the concept of a coordination service and provide an example. #coordination-service
    A: A coordination service is a third-party component that elects a leader node or set of leader nodes in a distributed system. Example algorithms used in coordination services include Paxos, Raft, and Zab. Apache ZooKeeper is an example of a distributed coordination service that provides features like access control, in-memory data storage, scalability, and guaranteed consistency within a specified time bound.

28. Describe the gossip protocol and its use in maintaining consistency. #gossip-protocol
    A: The gossip protocol is a technique used by distributed systems like Cassandra and DynamoDB to maintain consistency across data partitions or multiple data centers. It involves nodes exchanging state information with randomly selected peers, allowing data to propagate through the system in a decentralized manner. The gossip protocol helps ensure that updates are eventually replicated across all nodes, achieving eventual consistency without the need for a centralized coordination service.

29. What is random leader selection, and what are its potential drawbacks? #random-leader-selection
    A: Random leader selection is a simple algorithm used by some distributed systems to elect a leader node. It does not guarantee that only one leader is elected, potentially resulting in multiple leaders. While this approach is simpler than other leader election techniques, its potential drawbacks include the possibility of duplicate requests and unnecessary network traffic due to the presence of multiple leaders.

30. Differentiate between accuracy and consistency in the context of distributed systems. #accuracy #consistency
    A: Accuracy refers to ensuring that data values are correct and not approximations, while consistency refers to ensuring that data matches across different nodes or machines in a distributed system. While consistency focuses on data replication and synchronization, accuracy is more concerned with the correctness of the actual data values, regardless of their distribution across nodes.

31. What are estimation algorithms, and how do they trade off accuracy? #estimation-algorithms
    A: Estimation algorithms, such as HyperLogLog for cardinality estimation and count-min sketch for estimating event frequencies in data streams, trade off accuracy for lower complexity. These algorithms provide approximate results instead of exact values, sacrificing accuracy for improved performance or reduced resource requirements in certain scenarios.

32. How can caching affect accuracy, and what techniques can be used to mitigate this? #caching #accuracy
    A: Caches can become stale if the underlying data in the system is modified, leading to inaccuracies in the cached data. To mitigate this, techniques like refresh policies (periodically fetching the latest data) or cache invalidation (updating or deleting the associated cache key when data is modified) can be employed, although these increase complexity.

33. Explain the concept of continuous deployment (CD) and its benefits for maintainability. #continuous-deployment
    A: Continuous deployment (CD) is a practice that allows easy deployments and rollbacks of software updates or new features. It provides a fast feedback cycle, improving the maintainability of a system by enabling quick fixes or rollbacks in case of issues with a deployment. CD techniques like blue/green deployments (zero downtime deployments) and the use of static code analysis tools further enhance maintainability.

34. How can trading off cost be considered in system design, and what factors should be evaluated? #cost #cost-tradeoffs
    A: Cost can be traded off against other non-functional requirements in system design. For example, vertical scaling (more expensive but simpler) can be chosen over horizontal scaling for lower complexity, availability can be decreased by reducing redundancy to improve costs, or data centers further away from users can be used to reduce costs at the expense of higher latency. Factors to evaluate include implementation costs, maintenance costs, monitoring and alerting costs, future software atrophy and dependency updates, and potential decommissioning costs.

35. What are some security aspects that should be considered in system design? #security #security-aspects
    A: Security aspects to consider in system design include decisions around TLS termination versus encryption in transit, encryption at rest versus storing unencrypted data, implementing authentication and authorization mechanisms (e.g., OAuth 2.0, OpenID Connect), and incorporating rate limiting to prevent DDoS attacks.

36. Define Personally Identifiable Information (PII) and explain why it needs to be safeguarded. #pii #privacy
    A: Personally Identifiable Information (PII) is data that can be used to uniquely identify a customer, such as full name, government identifiers, addresses, email addresses, and bank account identifiers. PII must be safeguarded with access controls, encryption, hashing, and data masking techniques to comply with privacy regulations like GDPR and CCPA, and to protect customers' privacy.

37. What is the difference between privacy techniques for external and internal services? #external-vs-internal #privacy-techniques
    A: External services, which are accessible from outside the organization, should always implement security and privacy mechanisms such as access controls, encryption, hashing, and data masking to safeguard user data and comply with regulations. For internal services that only serve other internal services, it is generally considered best practice to implement security and privacy mechanisms by default as well.

38. Define the cloud native approach and its key components. #cloud-native #cloud-native-definition
    A: The cloud native approach is a methodology for building and running scalable applications in modern, dynamic environments like public, private, and hybrid clouds. Key components of cloud native include containers, service meshes, microservices, immutable infrastructure, and declarative APIs. This approach enables loosely coupled, resilient, manageable, and observable systems, allowing frequent and predictable changes with robust automation.

39. Explain the role of load balancing in achieving scalability. #load-balancing #scalability
    A: Load balancing is a crucial technique for achieving scalability in a system. It distributes traffic across multiple hosts, allowing the system to scale horizontally by adding more hosts as needed. Without load balancing, it would be challenging to distribute the load evenly across multiple hosts, leading to potential bottlenecks and limiting the system's ability to scale effectively.

40. How do sticky sessions contribute to maintaining state in stateful services? #sticky-sessions
    A: Sticky sessions are a load balancing technique that ensures requests from a particular client are sent to a specific host for a duration. This is especially useful for stateful services like e-commerce websites, social media platforms, or banking applications, where user session data (e.g., login information, preferences, shopping carts) needs to be maintained across requests. By sending all requests from a client to the same host, sticky sessions allow the host to maintain and access the client's state without the need for complex state management or replication mechanisms.

41. Describe the trade-offs between level 4 and level 7 load balancing. #load-balancing
    A: Level 4 load balancers operate at the transport layer (TCP) and make routing decisions based on address information extracted from the initial packets. They cannot inspect the contents of other packets or perform tasks like TLS termination or authentication. On the other hand, level 7 load balancers operate at the application layer (HTTP) and can make routing decisions based on the packet contents. They can perform additional functions like TLS termination and authentication. While level 7 load balancers offer more capabilities, they also introduce additional complexity and overhead compared to level 4 load balancers.

42. What is the purpose of circuit breakers? #circuit-breaker #fault-tolerance
    A: The purpose of circuit breakers is to improve fault tolerance. A circuit breaker stops a client from repeatedly attempting an operation that is likely to fail, preventing it from adding additional burden to an already overburdened system. By calculating the number of failed requests within a recent interval and stopping requests to downstream services if an error threshold is exceeded, circuit breakers save resources from being spent on requests that are likely to fail and allow the system to gracefully degrade instead of completely failing.

43. How does the fallback pattern contribute to fault tolerance and graceful degradation? #fallback-pattern #fault-tolerance
    A: The fallback pattern contributes to fault tolerance by providing a mechanism for graceful degradation when components fail. It involves executing an alternative code path, such as using cached responses or alternative services, when the primary service or component is unavailable or experiencing issues. This allows the system to continue functioning, albeit with potentially stale or less accurate data, rather than completely failing. The fallback pattern ensures that the system can maintain partial functionality even when parts of it fail, improving overall fault tolerance.

44. Explain the concept of checkpointing and its use in data processing operations. #checkpointing #fault-tolerance
    A: Checkpointing is a technique used in data processing operations where progress markers (checkpoints) are written after each subset of data points is processed and the result is successfully written. If a machine fails during data aggregation or processing, the replacement machine can resume processing from the most recent checkpoint instead of starting from the beginning. This ensures that data is not lost and the operation can continue from the last known good state, contributing to fault tolerance by allowing recovery from failures without losing progress.

45. What is the role of dead letter queues in handling failed write requests? #dead-letter-queue #fault-tolerance
    A: Dead letter queues play a crucial role in handling failed write requests in a fault-tolerant manner. Instead of immediately failing or losing data when a write request to a third-party API or service fails, the request is temporarily stored in a dead letter queue. This allows the system to retry these requests later, potentially after the issue with the third-party service has been resolved. By providing a mechanism to store and retry failed write operations, dead letter queues prevent permanent data loss or system failures, contributing to overall fault tolerance.

46. What is the purpose of forward error correction and error correction codes in the context of system design? #error-correction
    A: Forward error correction (FEC) and error correction codes (ECC) are techniques used to prevent errors in data transmission over unreliable or noisy communication channels. They involve encoding messages with redundant information, allowing the receiver to detect and correct errors that may have occurred during transmission. FEC and ECC are protocol-level concepts.
