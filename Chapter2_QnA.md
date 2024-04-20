# Chapter 2 (Q&A) - A typical system design interview flow

Questions and Answers:

1. What is the importance of clarifying functional and non-functional requirements in system design ? #Requirements #SystemDesign

   Answer: Clarifying functional and non-functional requirements ensures one understands the objectives and constraints of the system they are designing, allowing them to make informed design decisions.

2. What are some examples of non-functional requirements that should be discussed ? #NonFunctional #Discussion

   Answer: Non-functional requirements include scalability, latency, reliability, security, and maintainability. These aspects are crucial for ensuring the overall effectiveness and performance of the system.

3. Why is it essential to discuss logging, monitoring, alerting, and auditing during system design ? #Monitoring #Logging #SystemDesign

   Answer: Discussing these aspects ensures that the system is equipped to detect, diagnose, and respond to issues effectively, thus improving its reliability, performance, and security.

4. What is the significance of continuously improving a system during a system design? #Improvement #SystemDesign

   Answer: Continuous improvement ensures that the system remains adaptable, scalable, and efficient in meeting evolving requirements and challenges over time.

5. How can you consider both high-level architecture and low-level implementation details during system design ? #Architecture #Implementation #Demonstration

   Answer: Discuss architectural concepts and design patterns at a high level while also delving into specific implementation strategies, technologies, and trade-offs.

6. What does the term "observability" refer to in the context of system monitoring? #Observability #Monitoring

   Answer: Observability refers to the degree to which a system's internal state and behavior can be inferred from its externally visible outputs, such as logs, metrics, and traces.

7. Why is monitoring considered critical for every system? #Monitoring #SystemReliability

   Answer: Monitoring allows for proactive identification and resolution of issues, helps maintain system reliability, and provides valuable insights for performance optimization and capacity planning.

8. What are some potential failure scenarios that monitoring helps to identify in a system? #FailureScenario #Monitoring

   Answer: Monitoring can help identify failures such as downtime, performance degradation, errors, resource exhaustion, security breaches, and unexpected behavior in the system.

9. How can metrics, dashboards, and alerts contribute to the observability of a system? #Observability #Metrics #Dashboards #Alerts

   Answer: Metrics provide quantitative data about system behavior, dashboards offer visualizations of key metrics for quick insights, and alerts notify stakeholders about abnormal conditions, collectively enhancing the observability of the system.

10. What are some common OS metrics that can be included in a system's dashboard? #OSMetrics #Dashboard

    Answer: Common OS metrics include CPU utilization, memory usage, disk I/O, network traffic, and system uptime, which provide insights into the health and performance of the underlying infrastructure.

11. Why is it important for log entries to be structured and easy to parse? #LogEntries #Parsing

    Answer: Structured log entries facilitate automated analysis, searching, and troubleshooting, improving efficiency in diagnosing issues and extracting actionable insights from log data.

12. What are some common details captured in request-level logging in a system? #RequestLogging #Details

    Answer: Request-level logging typically captures information such as timestamp, client IP address, request method, endpoint URL, request parameters, response status code, and latency, aiding in request tracing and debugging.

13. What types of events should be logged to monitor system data integrity? #DataIntegrity #Logging

    Answer: Events such as data modifications, access control changes, system configuration changes, and integrity checks should be logged to monitor system data integrity and detect unauthorized or unintended actions.

14. What can be done to address possible silent failures in a system? #SilentFailures #SystemMaintenance

    Answer: To address silent failures, robust logging, monitoring, and alerting mechanisms should be implemented to detect abnormal behavior or deviations from expected outcomes, ensuring timely identification and resolution of issues.
