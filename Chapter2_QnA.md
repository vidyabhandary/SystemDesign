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

## Q&A: (From Section 2.5.3 till Section 2.7)

1. What is the purpose of a runbook, and what should it contain? #runbook #troubleshooting-procedures #automation #logging
   Answer: A runbook is a document that contains a list of alerts, possible causes, and procedures to troubleshoot and fix issues that may arise in a service. It should include steps for identifying and resolving various problems, as well as instructions for automating repetitive steps that can be easily executed through commands or scripts. The runbook should also include logging mechanisms to track the actions taken during the troubleshooting process.

2. What is considered runbook abuse, and why should it be avoided? #runbook-abuse
   Answer: Runbook abuse refers to the failure to implement automated recovery procedures when it is possible to do so. It should be avoided because manually executing a series of commands or scripts to solve a problem is inefficient and error-prone, especially during high-stress situations like outages. Automating these steps not only improves reliability and consistency but also reduces the workload on the on-call engineers.

3. What is the role of Site Reliability Engineering (SRE) teams in ensuring service reliability? #site-reliability-engineering #high-reliability #unit-testing #functional-testing #runbook-review
   Answer: Site Reliability Engineering (SRE) teams play a crucial role in ensuring the high reliability of critical services. They develop tools and processes to maintain and improve the reliability of these services. SRE teams may have deployment criteria for services, such as requiring high unit test coverage, a functional test suite that passes their review, and a well-written runbook with comprehensive coverage and problem descriptions, which has been vetted by the SRE team.

4. Why are blameless postmortems important after resolving incidents? #postmortem #root-cause-analysis #blameless-postmortem #self-healing #automation
   Answer: Blameless postmortems are important after resolving incidents because they encourage open discussion and problem-solving without fear of blame or punishment. They help identify the root causes of the incidents and preventive measures to avoid similar issues in the future. Postmortems should focus on analyzing patterns in the mitigation actions taken and exploring ways to automate those actions, introducing self-healing characteristics to the system.

5. What are the components of the ELK stack, and what are their respective roles? #elk-stack #logstash #elasticsearch #kibana #beats
   Answer: The ELK stack consists of Elasticsearch, Logstash, Kibana, and Beats. Logstash is used for collecting and managing logs. Elasticsearch is a search engine that indexes and searches logs. Kibana is a visualization and dashboarding tool that uses Elasticsearch as a data source, allowing users to search and visualize logs. Beats is a lightweight data shipper that sends data to Elasticsearch or Logstash in real-time.

6. What factors should be considered when choosing a monitoring tool? #monitoring-tools #features #platforms #resource-consumption #popularity #developer-support #learning-curve #configuration #integration #bugs #user-experience
   Answer: When choosing a monitoring tool, several factors should be considered, including the features offered (logging, monitoring, alerting, dashboarding), supported platforms (operating systems, network equipment, etc.), resource consumption, popularity (ease of finding familiar engineers), and developer support (frequency of updates). Additionally, subjective characteristics like the learning curve, difficulty of configuration, ease of integration with other software, number and severity of bugs, and user experience (UX) of the tool's interface should also be evaluated.

7. What is the purpose of time series databases (TSDB), and how do they help with data retention? #time-series-database #tsdb #downsampling #data-retention #compression #archiving
   Answer: Time series databases (TSDB) are optimized for storing and serving time-series data, such as logs generated continuously over time. They help with data retention by employing techniques like downsampling, which computes averages over defined intervals for old data, reducing storage requirements. Additionally, TSDBs may compress or archive older data to cheaper storage media like tapes or optical disks, further reducing storage costs while retaining historical data for analysis or compliance purposes.

8. What is the role of streaming and batch audits in ensuring data quality? #streaming-audit #batch-audit #silent-errors
   Answer: Streaming and batch audits play a crucial role in ensuring data quality. They involve implementing ETL (Extract, Transform, Load) jobs that validate recently added or modified data in databases. These audits are particularly useful for detecting silent errors, which are errors that went undetected by earlier validation checks during data processing or service request handling. By continuously or periodically auditing data, organizations can identify and address data quality issues proactively.

9. Explain the concept of anomaly detection and its applications. #anomaly-detection #machine-learning #anomaly-detection-algorithm #training-set #statistical-model #anomaly-probability #validation-set #test-set #model-selection #dataset-size #model-parameters #precision #recall
   Answer: Anomaly detection is a machine learning technique used to identify unusual or abnormal datapoints in a dataset. It involves feeding a continuous stream of data into an anomaly detection algorithm, which processes a training set to develop a statistical model. This model assigns a probability that a given datapoint is anomalous. The model is validated and tested using labeled validation and test sets, respectively. Anomaly detection has applications in ensuring data quality by detecting anomalous data patterns, as well as deriving analytical insights by identifying unusual changes in metrics that could indicate problems or changing market conditions. The configuration of anomaly detection models involves tuning parameters such as the choice of machine learning models, sizes of the training, validation, and test sets, and model parameters that balance precision and recall.

10. How can silent errors be detected and addressed? #silent-errors #batch-audit #database-auditing #alerts
    Answer: Silent errors, which occur when endpoints return success status codes despite errors, can be detected and addressed through batch audits. Organizations can write batch ETL jobs to audit recent changes to their databases and raise alerts when failed audits are detected. By continuously monitoring and auditing database changes, silent errors that may have gone unnoticed during earlier validation checks can be identified and addressed proactively.

11. What are the different techniques for implementing search functionality in applications? #search-bar #filtering #sql-search #like-operator #pattern-matching #limitations #client-side-library #match-sorter #small-datasets #search-engine #elasticsearch #scalability #large-datasets
    Answer: There are several techniques for implementing search functionality in applications:
12. SQL Database Search: Using the LIKE operator and pattern matching in SQL queries. However, this approach has limitations, such as difficulty in customization and lack of advanced features like boosting, fuzzy search, and text preprocessing.
13. Client-side Library: Using client-side libraries like match-sorter (JavaScript) to perform search and sorting on the client. This is suitable for small datasets (up to a few GB) but may not scale well for larger datasets.
14. Search Engine: Using a dedicated search engine like Elasticsearch, which is scalable and can handle large datasets efficiently. This approach offers advanced features like full-text search, fuzzy matching, and indexing capabilities.

15. How does Elasticsearch handle search queries? #full-text-search #fuzzy-matching #elasticsearch-index #exact-match #fuzzy-match
    Answer: Elasticsearch handles search queries by performing full-text searches against an Elasticsearch index, which is similar to a database in a relational database system. For exact matches, a query parameter can be used (`GET /index/_search?q=term`). For fuzzy matching, which allows approximate matches, a JSON request body can be sent (`GET /index/_search` with a JSON body containing the `match` query and `fuzziness` parameter). Elasticsearch supports advanced features like fuzzy matching to account for misspellings or typos in search terms or indexed data.

16. What are the steps involved in creating and maintaining an Elasticsearch index? #index-creation #document-ingestion #indexing #index-updates #bulk-api #index-mapping #reindexing
    Answer: The steps involved in creating and maintaining an Elasticsearch index include:
17. Index Creation: Ingesting and indexing the documents that should be searchable when users submit search queries.
18. Index Updates: Keeping the index updated with periodic or event-triggered indexing/delete requests using the Bulk API.
19. Index Mapping Changes: To change the index mapping, either create a new index and drop the old one or use Elasticsearch's reindexing operation.

20. What are the advantages and limitations of using Elasticsearch in place of a SQL database? #elasticsearch-as-sql #query-context #filter-context #eliminate-duplicate-storage #reduce-maintenance #schemaless #no-normalization #no-cqrs #no-acid #elasticsearch-sql
    Answer: Advantages of using Elasticsearch in place of a SQL database include eliminating duplicate storage requirements and reducing maintenance overhead. Elasticsearch can be used for both querying (with relevance scores) and filtering (yes/no matches). However, Elasticsearch has limitations.

Limitations of using Elasticsearch in place of a SQL database include:

- Schemaless nature: Elasticsearch does not have a schema or enforce data normalization like relational databases with primary and foreign keys.
- No CQRS or ACID: Elasticsearch does not provide Command Query Responsibility Segregation (CQRS) or ACID (Atomicity, Consistency, Isolation, Durability) properties found in relational databases.
- Verbosity: The Elasticsearch Query Language (EQL) is JSON-based and more verbose compared to SQL, which can be easier to learn and use, especially for non-technical users.

However, Elasticsearch SQL, introduced in version 6.3.0, provides a SQL-like syntax for querying Elasticsearch, potentially bridging the gap between Elasticsearch and relational databases in the future.

15. What are the benefits of implementing search functionality using Elasticsearch? #search-engine #elasticsearch #scalability #large-datasets #full-text-search #fuzzy-matching

Answer: Implementing search functionality using Elasticsearch offers several benefits:

1. Scalability: Elasticsearch is designed to be scalable and can handle large datasets efficiently, making it suitable for applications with extensive search requirements.

2. Full-text search: Elasticsearch provides robust full-text search capabilities, allowing users to search through large volumes of textual data quickly and accurately.

3. Fuzzy matching: Elasticsearch supports fuzzy matching, enabling users to find relevant results even when there are misspellings or typos in the search terms or indexed data.

4. Advanced features: Elasticsearch offers advanced features like boosting, weighting, text preprocessing (stemming, tokenization), and more, which enhance the search experience and enable more sophisticated search capabilities.

By leveraging Elasticsearch for search functionality, applications can provide users with a fast, accurate, and feature-rich search experience, even when dealing with large datasets.

## Q&A: (From Section 2.7 till Section 2.8)

1. 1. What are the key considerations when maintaining and extending an application over time? #software-development #requirements-management #maintenance #feature-development #feature-management

Answer: Maintaining and extending an application involves several key considerations:

- Continuous improvement and addressing evolving requirements, as applications are never truly complete.
- Identifying components that rely on external dependencies and plan for updates, security patches, and handling breaking changes.
- Determining which components require more frequent maintenance due to their reliance on external packages or rapid development pace.
- Evaluating potential future features and assessing their impact on the current system design and architecture.
- Identifying obsolete or unnecessary features and developing strategies for gracefully deprecating and decommissioning them while providing adequate user support and communication.

2. How can you ensure that your application remains usable and user-friendly? #usability #user-experience #metrics

Answer: To ensure an application remains usable and user-friendly, you should:

- Define usability metrics based on how users are intended to interact with the system, such as the average index of clicked search results, the number of help desk tickets, or the Net Promoter Score (NPS).
- Implement logging and data collection mechanisms to capture relevant user interaction data.
- Set up batch ETL processes to periodically compute usability metrics and update dashboards or reports for monitoring and analysis.
- Continuously monitor and address usability issues based on the collected metrics and user feedback.

3. What are some potential edge cases and extreme requirements that may arise, and how can they be addressed? #edge-cases #non-functional-requirements

Answer: Potential edge cases and extreme requirements that may arise include:

- Customizing payment systems to support different credit card requirements across countries, store credit, or coupon codes.
- Extending text search services to handle new data types like images, audio, and video.
- Handling room changes or finding available rooms across multiple hotels in a booking system.
- Integrating social networking features into existing services like news feed recommendations.
- Supporting millions of followers or recipients, and ensuring low-latency message delivery or accurate long-term data auditing.
- Increasing availability and fault tolerance for mission-critical services, handling component failures, and implementing mitigation strategies.
- Optimizing costs while maintaining performance and latency requirements.
- Enabling application portability between cloud and on-premises environments, considering trade-offs like increased complexity and costs.

These edge cases and extreme requirements may necessitate redesigning the architecture, making trade-offs, or implementing additional features or capabilities to accommodate them.

4. What is the significance of revisiting architectural decisions, and when should it be done? #architecture #design-decisions

Answer: Revisiting architectural decisions is crucial because:

- Initial architectural decisions may need to be re-evaluated as new insights or evolving requirements emerge.
- Changing architectural decisions can have a significant impact on the existing system, and their implications should be carefully assessed.
- Revisiting architectural decisions allows for course correction and ensures that the system remains aligned with the project's goals and requirements.

Architectural decisions should be revisited periodically, especially during major milestones or when significant changes in requirements or constraints arise.

5. How can cloud-native concepts and technologies be leveraged to address non-functional requirements? #cloud-native #microservices #containers #automation

Answer: Cloud-native concepts and technologies can be leveraged to address non-functional requirements in the following ways:

- Adopting a microservices architecture can improve scalability, fault tolerance, and flexibility in addressing evolving requirements.
- Implementing service mesh and sidecars (e.g., Istio) can provide shared services like traffic management, observability, and security across microservices.
- Containerization (e.g., Docker) can facilitate consistent and portable application deployment across different environments.
- Orchestration tools (e.g., Kubernetes) can automate container deployment, scaling, and management, improving efficiency and reliability.
- Automation tools (e.g., Skaffold, Jenkins) can streamline development, testing, and deployment processes, reducing manual effort and increasing agility.
- Infrastructure as Code (e.g., Terraform, Helm) can enable consistent and repeatable provisioning and management of infrastructure resources across different environments.
