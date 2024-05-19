# Chapter 3 - Non-Functional Requirements (Summary)

# Functional and Non-Functional Requirements #requirements

- **Functional requirements** describe the inputs and outputs of the system. #functional-requirements
- **Non-functional requirements** include scalability, availability, performance, fault tolerance, security, privacy, accuracy, consistency, cost, complexity, maintainability, debuggability, and testability. #non-functional-requirements
- **Clarifying requirements** is necessary to avoid misunderstandings and inadequate solutions. #clarifying-requirements
- **Trade-offs** among non-functional requirements are common in system design. #trade-offs

# Scalability #scalability

- **Horizontal scaling** is spreading processing and storage across multiple hosts for true scalability. #horizontal-scaling
- **Load balancing** distributes traffic across multiple hosts, can be level 4 (transport layer) or level 7 (application layer). #load-balancing
- **Sticky sessions** send requests from a client to a specific host for stateful services. #sticky-sessions

# Availability #availability

- **High availability** is often desired, trading off consistency and latency if necessary.
- Techniques include replication, circuit breakers, fallback patterns, and asynchronous communication. #high-availability

# Fault Tolerance #fault-tolerance

- **Replication and redundancy** with leader-follower or multi-master models. #replication #redundancy
- **Circuit breaker** stops clients from repeatedly attempting operations likely to fail. #circuit-breaker
- **Fallback pattern** executes alternative code paths for graceful degradation. #fallback-pattern
- **Checkpointing** allows resuming data processing operations after failures. #checkpointing

# Performance/Latency and Throughput #performance #latency #throughput

- **Reducing latency** techniques include deploying closer to users, CDNs, caching, batch/streaming, and minimizing message sizes. #reducing-latency

# Consistency #consistency

- Understand the difference between **ACID consistency** and **CAP consistency (linearizability)**. #acid #cap
- **Eventual consistency** trades off consistency for availability, scalability, and latency. #eventual-consistency
- Techniques include full mesh, coordination service, distributed cache, gossip protocol, and random leader selection. #full-mesh #coordination-service #distributed-cache #gossip-protocol #random-leader-selection

# Accuracy #accuracy

- **Accuracy vs. consistency**: Accuracy refers to data values being correct, while consistency refers to data matching across nodes. #accuracy-vs-consistency
- Estimation algorithms trade off accuracy for lower complexity. #estimation-algorithms

# Complexity and Maintainability #complexity #maintainability

- **Minimizing complexity** by clarifying requirements, separating components, and using common services. #minimizing-complexity
- **Continuous deployment (CD)** allows easy deployments, rollbacks, and fast feedback cycles. #continuous-deployment
- **Outage prevention and mitigation** through replication, failover, runbooks, logging, and monitoring. #outage-prevention #outage-mitigation

# Cost #cost

- **Trading off cost** against other non-functional properties, such as availability and latency. #cost-tradeoffs
- **Cost considerations** include implementation, maintenance, monitoring, software atrophy, and decommissioning. #cost-considerations

# Security #security

- **Security aspects** include TLS termination, encryption, authentication, authorization, and rate limiting. #security-aspects

# Privacy #privacy

- **Personally Identifiable Information (PII)** must be safeguarded with access controls, encryption, hashing, and data masking. #pii
- **External services** should implement security and privacy mechanisms, while **internal services** should follow best practices. #external-vs-internal

# Cloud Native #cloud-native

- **Cloud native definition**: Building and running scalable applications using containers, service meshes, microservices, immutable infrastructure, and declarative APIs. #cloud-native-definition
- **Benefits** include resilience, manageability, observability, and enabling frequent and predictable changes. #cloud-native-benefits

These key points highlight the essential concepts, techniques, and trade-offs related to various non-functional requirements in system design, as well as the cloud-native approach to addressing these requirements.

References:

1. [XKCD - Modern Digital Infrastructure](https://xkcd.com/2347/)

2. [xz Utils backdoor](https://www.youtube.com/watch?v=WaDdEosS520)

3. [Avoiding fallback in distributed systems](https://aws.amazon.com/builders-library/avoiding-fallback-in-distributed-systems/)

4. [LBaaS](https://github.com/vidyabhandary/til/blob/master/misc/LBaaS.md)

5. [Key differences between MTTR, MTBF, RTO, and RPO](https://github.com/vidyabhandary/til/blob/master/misc/RTO_RPO_MTTR_MTBF.md)

6. [Flash Boys by Michael Lewis](https://vidyabhandary.blogspot.com/2015/01/book-review-flash-boys-by-michael-lewis.html)
