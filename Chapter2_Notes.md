# Chapter 2 - A typical system design interview flow (Summary)

Study Notes:

# Principles of System Design

1. Clearly define the functional and non-functional requirements (e.g., queries per second, 99th percentile latency). #Requirements #Clarification #Scalability

2. Recognize that every aspect involves trade-offs. Improvements in scalability, consistency, or latency will increase complexity, cost, and necessitate additional security, logging, monitoring, and alerting mechanisms. #TradeOffs #Complexity #Cost #Security

3. Incorporate logging, monitoring, alerting, and auditing mechanisms into the design. #Logging #Monitoring #Alerting #Auditing

4. Address testing, maintainability, debuggability, complexity, security, and privacy concerns. #Testing #Maintainability #Debuggability #Complexity #Security #Privacy

5. Consider graceful degradation and failure handling in the overall system and every component, including silent and disguised failures. #GracefulDegradation #FailureHandling #TrustIssues

## Clarifying Requirements

1. Consider user categories/roles: #UserRoles
   a. Identify who will use the system and how. (e.g., manual/consumer, programmatic/enterprise).
   b. Determine if the users are technical or non-technical.
   c. List the various user roles (e.g., buyer, seller, poster, viewer, developer, manager).
   d. Pay close attention to numerical requirements (e.g., how many news items, how much time).
   e. Inquire about internationalization (i18n) and localization (L10n) support, language requirements, postal address formats, currency support, etc.

2. Based on the user categories, clarify the scalability requirements. Estimate the number of daily active users and the daily or hourly request rates. #Scalability

3. Determine which data should be accessible to which users. Define authentication and authorization roles and mechanisms, #Authentication #Authorization response body contents, and data retrieval frequency (real-time, monthly reports, etc.).

4. Discuss potential use cases involving search functionality. #Search

5. Explore potential machine learning requirements, including support for experimentation (e.g., A/B testing, multi-armed bandit). #MachineLearning #ABTesting

6. Continually ask, "Are there any other user requirements?" and brainstorm possibilities. Do not rely solely on the stakeholders to provide all the requirements.

7. Discuss non-functional requirements. #NonFunctionalRequirements

### Common API Endpoints #APIEndpoints

- **Health** (`GET /health`): Test endpoint, 4xx or 5xx response indicates system issues.
- **Authentication**: Endpoints for user signup and login. OpenID Connect is a common authentication protocol.
- **User and Content Management**: Endpoints to manage user details and allow users to flag/report inappropriate content.

## Connections and Data Processing

1. Break down the system into components (libraries or services).
2. Illustrate the connections between components.
3. Incorporate logging, monitoring, and alerting mechanisms. #Logging #Monitoring #Alerting
4. Address security considerations.
5. Analyze fault tolerance. #FaultTolerance Determine what can go wrong with each component (e.g., network delays, inconsistency, lack of linearizability).
6. Outline strategies to prevent and/or mitigate potential issues and improve fault tolerance.

## Designing the Data Model #DataModeling

1. Determine whether you are designing the data model from scratch or using existing databases. #DatabaseDesign

2. Sharing databases between services is generally considered an anti-pattern. It is preferable to build dedicated API endpoints designed for programmatic customers and implement batch/streaming ETL pipelines to and from other databases as required.
3. A example technique to prevent concurrent user update conflict can be by using an SQL table with timestamp and user columns for locking configurations.

### Problems with shared databases:

- Queries from various services may compete for resources, potentially locking tables for extended periods.
- Schema migrations become more complex and may break other services 'Data Access Object' (DAO) code.
- Services are restricted to using specific database technologies, regardless of their suitability for the given use cases.
- The new system has to utilize the request and response bodies of the API endpoints to design the schema, mapping each body to a table's schema and combining read (GET) and write (POST, PUT) requests of the same paths to the same table.

## The Importance of Monitoring #Monitoring

1. Monitoring is crucial for every system to provide visibility into the customer experience.
2. It helps to identify bugs, degradations, unexpected events, and weaknesses in meeting current and future functional and non-functional requirements.

### Observability #Observability

Observability is a measure of how well-instrumented the system is and how easily you can understand what's happening within it. Without logging, metrics, and tracing, the system is opaque.

**Alerting:** #Alerting

1. Latency - Set up alerts for latency exceeding defined Service Level Agreements (SLAs) (e.g., more than 1 second, 99th percentile over sliding windows).
2. Traffic - Measured in HTTP requests per second. Set up alerts for various endpoints if traffic exceeds expected levels.
3. Errors - Set up high-priority alerts for 4xx or 5xx response codes. Trigger alerts on failed audits.
4. Saturation - Set up alerts if utilization targets (CPU, memory, I/O, storage) are reached or may be exhausted within a certain time frame.

**Three instruments of monitoring and alerting:** #Metrics #Dashboards #Alerting

- Metrics: Variables measured (error count, latency, processing time).
- Dashboards: Summary view of a service's core metrics.
- Alerts: Notifications sent to service owners in reaction to problems.

Operating System (OS) metrics like CPU utilization, memory utilization, disk utilization, and network I/O can be included in dashboards and used for hardware allocation tuning or detecting memory leaks.

Log entry structure: #Logging

- Unique identifier to trace requests across services and share between users and developers.
- Small, easy-to-read, and useful.
- Consistent time zone and time format.
- Categorize log entries (e.g., debug, info, error).
- Do not log private or sensitive information (Personally Identifiable Information - PII).

Common log entries: #Logging

- Host logging: CPU, memory utilization, network I/O.
- Request-level logging: Latency, who/when made the request, function name and line number, request path and parameters, headers and body, return status code and body (including error messages).

Log events to monitor how well the system meets unique functional and non-functional requirements (e.g., cache faults, hits, misses).

In enterprise systems, provide users with access to monitoring tools or build monitoring tools specifically for users (e.g., customers can create dashboards to track request state, filter and aggregate metrics and alerts by categories like URL paths).

Address potential silent failures due to bugs in application code or dependencies that allow 2xx response codes when they should be 4xx or 5xx. This may indicate the need for improvements in logging and monitoring.

## Key Takeaways (till 2.5.3)

1. Follow the principles of system design, such as clarifying requirements, incorporating logging, monitoring, alerting, auditing, testing, maintainability, graceful degradation, failure handling, and continuous improvement. #SystemDesign #Requirements #TimeManagement #Logging #Monitoring #Alerting #Auditing #Testing #Maintainability #GracefulDegradation #FailureHandling #ContinuousImprovement
2. Collaborate with fellow professionals to enhance communication and articulation of system design concepts. #Collaboration #Communication #SystemDesign
3. Clearly define functional and non-functional requirements, considering user categories, roles, scalability, data access, search, and machine learning needs. #Requirements #FunctionalRequirements #NonFunctionalRequirements #Scalability #DataAccess #Search #MachineLearning
4. Draft API specifications based on functional requirements, considering common endpoints like health, authentication, and user/content management. #API #APISpecifications #Endpoints #Authentication #UserManagement #ContentManagement
5. Design the data model, considering the advantages of creating a new schema over shared databases across services. #DataModel #SchemaDesign #Database #Services
6. Implement techniques to prevent concurrent user update conflicts, such as locking configurations using SQL tables with timestamps and user columns. #Concurrency #SQL #Timestamps #Locking #UserUpdateConflicts
7. Prioritize monitoring for visibility into the customer experience, identifying bugs, degradations, and weaknesses in meeting requirements. #Monitoring #CustomerExperience #Bugs #Degradations #Weaknesses
8. Understand observability, and implement logging, metrics, dashboards, and alerts for various aspects like latency, traffic, errors, and saturation. #Observability #Logging #Metrics #Dashboards #Alerts #Latency #Traffic #Errors #Saturation
9. Structure log entries properly, including unique identifiers, consistent time formats, and categorization, while avoiding sensitive information. #Logging #DataStructure #UniqueIdentifiers #TimeFormats #Categorization #SensitiveInformation
10. Monitor common aspects like host utilization, request-level details, and system-specific events and requirements. #Monitoring #HostUtilization #RequestDetails #SystemEvents #SystemRequirements
11. Address silent failures through improved logging and monitoring. #SilentFailures #Logging #Monitoring
12. Implement batch and streaming audit jobs to validate data integrity, with alerts for failed validations. #BatchProcessing #Streaming #AuditJobs #DataIntegrity #Alerts #Validation

References -

- [Designing Data Intensive Applications by Martin Kleppman - Chapter 1: Reliable, Scalable, and Maintainable Applications](https://dataintensive.net/)
  #### P99
  If the 99th percentile response time is 1.5 seconds, that means 99 out of 100 requests take less than 1.5 seconds, and 1 out of 100 requests take 1.5 seconds or more
