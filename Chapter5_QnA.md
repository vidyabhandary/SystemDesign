# Chapter 5 - Distributed Transactions - (Q&A)

1. **Q: How can a distributed system maintain data consistency when multiple services are involved in a single unit of work?**
   A: Distributed systems can maintain data consistency by implementing transactions that group reads and writes into atomic units with ACID properties. This ensures that all operations either complete successfully or are rolled back. Additionally, using patterns like event sourcing, Change Data Capture (CDC), or sagas can help manage consistency across services.
   #distributedsystems #dataconsistency

2. **Q: Compare and contrast Event-Driven Architecture (EDA) with traditional request-response patterns in distributed systems.**
   A: EDA promotes asynchronous, non-blocking interactions where components announce events that have occurred, rather than directly requesting work to be done. This leads to looser coupling, improved scalability, and better responsiveness compared to traditional request-response patterns. EDA reduces resource consumption and improves system availability, especially during traffic spikes. However, it may introduce complexity in tracking the overall system state and ensuring eventual consistency.
   #EDA #distributedsystems

3. **Q: Explain the concept of event sourcing and discuss its advantages and challenges.**
   A: Event sourcing is a pattern where all changes to application state are stored as a sequence of events in an append-only log. Advantages include:

   1. Providing a complete audit trail
   2. Ability to reconstruct past states
   3. Flexibility in evolving business logic
      Challenges include:
   4. Increased system complexity
   5. Higher storage requirements
   6. Potential performance issues with event replay as logs grow
      Event sourcing is particularly useful for systems requiring strong audit capabilities or complex domain models.
      #eventsourcing #systemdesign

4. **Q: How does Change Data Capture (CDC) differ from event sourcing, and in what scenarios might you choose one over the other?**
   A: CDC logs data change events from a source system (usually a database) to a change log event stream, while event sourcing uses events as the primary source of truth for the system state. CDC is typically used for synchronizing data changes between services or databases, offering lower latency and easier integration with existing systems. Event sourcing is more suitable when you need a complete history of all state changes or when you want to derive multiple views from the same event stream. Choose CDC for simpler data synchronization needs and event sourcing for more complex domain modeling or when full auditability is required.
   #CDC #eventsourcing

5. **Q: Describe the saga pattern and explain how it helps in managing distributed transactions.**
   A: The saga pattern is a way to manage long-lived transactions that span multiple services in a distributed system. It breaks down a complex transaction into a sequence of smaller, local transactions, each with a compensating transaction to undo its effects if needed. If any step fails, the saga executes compensating transactions in reverse order to maintain data consistency. This pattern helps overcome the challenges of maintaining ACID properties across distributed services and allows for better scalability and fault tolerance in microservices architectures.
   #sagapattern #distributedtransactions

6. **Q: Compare choreography and orchestration approaches in implementing sagas. What are the trade-offs between them?**
   A: Choreography and orchestration are two approaches to implementing sagas:

   **Choreography**:

   - Services communicate directly via message broker topics
   - Parallel execution of steps
   - Lower latency and less resource-intensive
   - Can become complex and hard to manage in large systems
   - More difficult to track the overall process

   **Orchestration**:

   - Uses a central orchestrator to coordinate all steps
   - Linear execution of steps
   - Easier to understand and maintain, especially in complex systems
   - Higher latency and more resource usage due to central coordination
   - Clearer separation of concerns between services

   The choice depends on system complexity, performance requirements, and team structure. Choreography is often better for simpler systems or when high performance is crucial, while orchestration shines in more complex scenarios or when clear process visibility is important.
   #sagapattern #systemdesign
