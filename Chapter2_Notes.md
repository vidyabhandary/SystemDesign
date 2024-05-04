# Chapter 2 - A typical system design interview flow (Summary)

## Study Notes: (Till Section 2.5.3)

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

---

## Study Notes: (From Section 2.5.3 till Section 2.7)

# Responding to Alerts and Runbooks

## Preparation and Structure

- Development teams maintain services and set up on-call schedules for high-urgency alerts. #on-call-schedule #high-urgency-alerts
- Runbooks contain a list of alerts, possible causes, and procedures to troubleshoot and fix issues. #runbook #troubleshooting-procedures
- Automate repetitive steps that can be easily executed through commands or scripts, along with logging. #automation #logging
- Failure to implement automated recovery when possible is considered runbook abuse. #runbook-abuse
- Display relevant metrics on dashboards if manual investigation is required. #dashboard #metrics

## Site Reliability Engineering (SRE) Involvement

- SRE teams develop tools and processes to ensure high reliability of critical services. #site-reliability-engineering #high-reliability
- SRE teams may have deployment criteria, such as:
  - High unit test coverage #unit-testing
  - Functional test suite passing SRE review #functional-testing
  - Well-written runbook with comprehensive coverage and problem descriptions, vetted by SRE team #runbook-review

## Post-Incident Review

- Author a postmortem after resolving incidents, identifying root causes and preventive measures. #postmortem #root-cause-analysis
- Postmortems should be blameless to encourage open discussion and problem-solving. #blameless-postmortem
- Identify patterns in mitigation actions and automate them to introduce self-healing characteristics. #self-healing #automation

# Application-Level Logging Tools

## Common Logging Tools

- ELK (Elasticsearch, Logstash, Kibana) stack and Splunk are popular logging tools. #elk-stack #splunk
- Logstash collects and manages logs. #logstash
- Elasticsearch indexes and searches logs. #elasticsearch
- Kibana visualizes and dashboards logs. #kibana
- Beats ships data to Elasticsearch or Logstash in real-time. #beats

## Monitoring Tools

- Monitoring tools may be proprietary or open-source (FOSS). #monitoring-tools #proprietary #open-source
- Tools differ in features (logging, monitoring, alerting, dashboarding), supported platforms, resource consumption, popularity, and developer support. #features #platforms #resource-consumption #popularity #developer-support
- Subjective characteristics include learning curve, configuration difficulty, integration ease, bug severity, and user experience (UX). #learning-curve #configuration #integration #bugs #user-experience

## Open-Source Monitoring Tools

- Prometheus + Grafana (monitoring and visualization) #prometheus #grafana
- Sensu (monitoring with Redis storage, third-party alerting) #sensu #redis #third-party-alerting
- Nagios (monitoring and alerting) #nagios
- Zabbix (monitoring with dashboard) #zabbix

## Proprietary Monitoring Tools

- Examples: Splunk, Datadog, New Relic #splunk #datadog #new-relic

## Time Series Databases (TSDB)

- Optimized for storing and serving time-series data, such as logs. #time-series-database #tsdb
- Downsampling computes averages over intervals to reduce storage for old data. #downsampling #data-retention
- Compression or archiving to cheaper storage for older data. #compression #archiving
- Examples: Graphite, Prometheus, OpenTSDB, InfluxDB. #graphite #prometheus #opentsdb #influxdb

# Streaming and Batch Audit of Data Quality

## Data Quality

- Ensuring data represents the real-world construct it refers to and can be used for intended purposes. #data-quality

## Auditing

- Implement streaming and batch ETL jobs to validate recently added or modified data. #streaming-audit #batch-audit
- Useful for detecting silent errors (undetected by earlier validation checks). #silent-errors

# Anomaly Detection

## Concept

- Machine learning technique to detect unusual datapoints. #anomaly-detection #machine-learning
- Input data stream is processed by an anomaly detection algorithm. #anomaly-detection-algorithm
- Algorithm develops a statistical model after processing a training set. #training-set #statistical-model
- Model assigns probability of a datapoint being anomalous. #anomaly-probability
- Model is validated and tested using labeled validation and test sets. #validation-set #test-set

## Configuration

- Tunable parameters include machine learning models, dataset sizes, and model parameters (precision vs. recall). #model-selection #dataset-size #model-parameters #precision #recall

# Silent Errors and Auditing

- Silent errors occur when endpoints return success status despite errors. #silent-errors
- Write batch ETL jobs to audit recent database changes and raise alerts on failed audits. #batch-audit #database-auditing #alerts

# Search Bar

## Overview

- Common feature in many applications to quickly find desired data. #search-bar
- Can be a single search bar or include additional filtering components. #filtering

## Implementation Techniques

1. **SQL Database Search**

   - Use LIKE operator and pattern matching. #sql-search #like-operator #pattern-matching
   - Limitations: Difficult customization, no advanced features (boosting, fuzzy search, text preprocessing). #limitations

2. **Client-side Library**

   - Use libraries like match-sorter (JavaScript). #client-side-library #match-sorter
   - Suitable for small datasets (up to a few GB). #small-datasets

3. **Search Engine**
   - Use search engines like Elasticsearch. #search-engine #elasticsearch
   - Scalable and can handle large datasets. #scalability #large-datasets

## Search Bar Implementation with Elasticsearch

### Elasticsearch Query

- Basic full-text search with fuzzy matching. #full-text-search #fuzzy-matching
- Query against an Elasticsearch index. #elasticsearch-index
- Exact match: `GET /index/_search?q=term` #exact-match
- Fuzzy match: `GET /index/_search` with JSON request body. #fuzzy-match

### Elasticsearch Index and Ingestion

- Create an index by ingesting and indexing documents to be searched. #index-creation #document-ingestion #indexing
- Keep index updated with periodic or event-triggered indexing/delete requests using Bulk API. #index-updates #bulk-api
- Change index mapping by creating a new index and dropping the old one, or using reindexing operation. #index-mapping #reindexing

### Using Elasticsearch in Place of SQL

- Elasticsearch can be used like SQL. #elasticsearch-as-sql
- Query context (relevance scores) vs. filter context (yes/no matches). #query-context #filter-context
- Eliminates duplicate storage and maintenance overhead of SQL database. #eliminate-duplicate-storage #reduce-maintenance
- Schemaless, no normalization or relations (primary/foreign keys). #schemaless #no-normalization
- No Command Query Responsibility Segregation (CQRS) or ACID. #no-cqrs #no-acid
- Elasticsearch SQL introduced in 6.3.0 with SQL-like syntax. #elasticsearch-sql

| Tool                 | Features                                    | Platforms | Resource Consumption | Popularity | Developer Support |
| -------------------- | ------------------------------------------- | --------- | -------------------- | ---------- | ----------------- |
| ELK Stack            | Logging, Monitoring, Alerting, Dashboarding | Various   | Varies               | High       | Active            |
| Prometheus + Grafana | Monitoring, Visualization                   | Various   | Moderate             | High       | Active            |
| Sensu                | Monitoring, Third-party Alerting            | Various   | Moderate             | Moderate   | Active            |
| Nagios               | Monitoring, Alerting                        | Various   | Moderate             | High       | Legacy            |
| Zabbix               | Monitoring, Dashboarding                    | Various   | Moderate             | Moderate   | Active            |
| Splunk               | Logging, Monitoring, Alerting, Dashboarding | Various   | High                 | High       | Commercial        |
| Datadog              | Monitoring, Alerting, Dashboarding          | Various   | Moderate             | High       | Commercial        |
| New Relic            | Monitoring, Alerting, Dashboarding          | Various   | Moderate             | High       | Commercial        |

## Subjective Characteristics of Monitoring Tools

| Tool                 | Learning Curve | Configuration Difficulty | Integration Ease | Bug Severity | User Experience (UX) |
| -------------------- | -------------- | ------------------------ | ---------------- | ------------ | -------------------- |
| ELK Stack            | Moderate       | Moderate                 | Moderate         | Low          | Good                 |
| Prometheus + Grafana | Moderate       | Moderate                 | Good             | Low          | Good                 |
| Sensu                | Moderate       | Moderate                 | Moderate         | Moderate     | Moderate             |
| Nagios               | Steep          |                          |                  |              |                      |
|                      |                |                          |                  |              |                      |

Maintaining and Extending Applications

## Continuous Improvement and Evolving Requirements #software-development #requirements-management

- Applications are never truly complete; there are always new requirements and user feedback to address.
- Ongoing efforts are needed to develop, maintain, deprecate, and decommission features based on changing needs.
- Monitor API traffic and request contents to inform scaling and development decisions.

### Maintenance Considerations #maintenance #dependencies #upgrades

- Identify system components relying on external software packages or dependencies.
- These components may require more frequent maintenance to keep up with updates and security patches.
- Plan for versioning and upgrade strategies to handle breaking changes introduced by dependencies.
- Implement thorough testing, feature flags, and versioning practices to mitigate the impact of breaking changes.
- In-house developed components may require less frequent updates but still need processes for bug fixes, tech debt repayment, and refactoring.

### Future Feature Development #feature-development #system-design

- Discuss potential features that may need to be developed in the future.
- Assess how these new features would impact the current system design and architecture.
- Determine if modifications or extensions to the existing system are required.

### Feature Deprecation and Decommissioning #feature-management #user-support

- Identify features that may become obsolete or unnecessary in the future.
- Develop strategies for gracefully deprecating and decommissioning these features.
- Determine the appropriate level of user support and communication required during this process.
- Provide clear documentation and guidance to users affected by the changes.

## Supporting Additional User Types #user-types #scalability

- Evaluate the potential need to extend the service to support different user categories (e.g., consumers, enterprises, manual users, programmatic users).
- Discuss approaches for extending current services or building new dedicated services.
- Analyze the trade-offs between these approaches, considering factors like complexity, security boundaries, code reuse, and maintainability.

## Revisiting Architectural Decisions #architecture #design-decisions

- Revisit and re-evaluate alternative architectural decisions made during the initial design phase.
- Discuss these decisions in greater detail, considering new insights or evolving requirements.
- Assess the potential impact of changing architectural decisions on the existing system.

## Usability and User Feedback #usability #user-experience #metrics

- Define usability metrics based on how users are intended to interact with the system.
- Implement logging and data collection mechanisms to capture relevant user interaction data.
- Set up batch ETL processes to compute usability metrics periodically and update dashboards or reports.

### Usability Metric Examples

- **Search Engine**: Average index of the result list that users click on (lower is better, indicating users find desired results near the top).
- **Help Desk Tickets**: Number of help desk tickets created per day or week (lower is better, indicating a more self-service application).
- **Net Promoter Score (NPS)**: Percentage of users rating their likelihood to recommend the application as 9 or 10 (out of 10) minus the percentage rating 6 or below.

## Handling Edge Cases and Extreme Requirements #edge-cases #non-functional-requirements

- Anticipate edge cases and new constraints that may be introduced later in the development process.
- Discuss strategies to fulfill these edge cases or redesign the architecture to accommodate them.
- Examples of potential edge cases and extreme requirements:
  - **Payment Systems**: Supporting different credit card payment requirements across countries, store credit, coupon codes, etc.
  - **Search Services**: Extending text search to images, audio, and video.
  - **Booking Systems**: Handling room changes or finding available rooms across multiple hotels.
  - **Social Networking Features**: Integrating social networking capabilities into existing services like news feed recommendations.
  - **Scalability and Performance**: Supporting millions of followers/recipients, low-latency message delivery, accurate long-term data auditing.
  - **Availability and Fault Tolerance**: Increasing availability for mission-critical services, handling component failures, and implementing mitigation strategies.
  - **Cost Optimization**: Exploring trade-offs to reduce costs while maintaining performance and latency requirements.
  - **Portability**: Enabling the application to be easily moved between cloud and on-premises environments, considering trade-offs like increased complexity and costs.

## Cloud-Native Concepts #cloud-native #microservices #containers #automation

- Discuss addressing non-functional requirements using cloud-native concepts and technologies:
  - Microservices architecture
  - Service mesh and sidecars for shared services (e.g., Istio)
  - Containerization (e.g., Docker)
  - Orchestration (e.g., Kubernetes)
  - Automation (e.g., Skaffold, Jenkins)
  - Infrastructure as Code (e.g., Terraform, Helm)

References:

1. [How Shazam Works](https://www.youtube.com/watch?v=kMNSAhsyiDg)

2. [How to Reindex One Billion Documents in One Hour at SoundCloud](https://developers.soundcloud.com/blog/how-to-reindex-1-billion-documents-in-1-hour-at-soundcloud)

3. [Git: Best Practices for Teams](https://www.youtube.com/watch?v=Hd_BMpn4sBA)
