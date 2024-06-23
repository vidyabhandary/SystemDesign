# Chapter 5 - Distributed Transactions (Summary)

1. **Distributed Transactions and Data Consistency**

- Multiple services in a single unit of work can lead to data inconsistency if any service fails
- Transactions group reads and writes into atomic units with ACID properties

2. **Event-Driven Architecture (EDA)**

- Promotes loose coupling, scalability, and responsiveness
- Reduces resource consumption and improves system availability compared to direct service requests

3. **Event Sourcing**

- Stores data changes as events in an append-only log
- Provides a complete audit trail and flexibility in changing business logic
- Challenges include increased complexity and higher storage requirements

4. **Change Data Capture (CDC)**

- Logs data change events to a change log event stream
- Ensures consistency and provides lower latency compared to event sourcing

5. **Transaction Supervisor**

- Ensures transaction completion or compensation
- Automate compensating transactions cautiously and with extensive testing

6. **Saga Pattern**

- Long-lived transaction composed of a sequence of smaller transactions
- All transactions must complete or compensating transactions are executed
- Two main approaches: Choreography and Orchestration

7. **Choreography Saga**

- Services communicate directly via message broker topics
- Parallel execution of steps, but can become complex in large systems

8. **Orchestration Saga**

- Central orchestrator coordinates all steps in the saga
- Linear execution of steps, easier to understand and maintain in complex systems

9. **Key Trade-offs: Choreography vs. Orchestration**

- Choreography: Lower latency, less resource-intensive, but more complex at scale
- Orchestration: Easier to manage and understand, but higher latency and resource usage

#DistributedSystems #EventDrivenArchitecture #Microservices #SystemDesign #SoftwareArchitecture

References:

1. [Saga Pattern](https://www.youtube.com/watch?v=d2z78guUR4g)
