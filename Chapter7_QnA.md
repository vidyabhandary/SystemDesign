# Chapter 7 - Design Craigslist - (Q&A)

1. Q: How does the system handle the different needs of viewers and posters in a Craigslist-like platform?
   A: The system distinguishes between two main user types: viewers and posters, each with distinct functionality. Posters can create and delete posts, search their own posts, and include details such as title, description, price, location, and photos. They also receive email notifications for post renewal every 7 days. Viewers, on the other hand, can view all posts or search within a city (limited to the last 7 days), apply filters to results, view individual post details, contact posters, and report fraudulent or misleading posts.
   #UserTypes #PosterFunctionality #ViewerFunctionality

2. Q: What are the key non-functional requirements for this system, and why are they important?
   A: The key non-functional requirements are:

   1. Scalability: Supporting up to 10 million users per city, which is crucial for handling large user bases in populous areas.
   2. Availability: 99.9% uptime, ensuring the service is reliably accessible to users.
   3. Performance: Near real-time post visibility and 1 second P99 for search and viewing, which provides a responsive user experience.
   4. Security: Poster authentication is required to prevent abuse and maintain trust in the platform.
      These requirements are important because they ensure the system can handle high loads, remain accessible, provide a smooth user experience, and maintain security standards.
      #NonFunctionalRequirements

3. Q: Describe the pros and cons of using a monolithic architecture for this system.
   A: Pros of a monolithic architecture:

   - Simple to implement and maintain
   - Reduced network overhead
   - Easier to manage as a single unit

   Cons of a monolithic architecture:

   - Limited flexibility, especially for mobile apps
   - Potential content duplication
   - Challenges with analytics on post data
   - Harder to scale individual components

   To mitigate some cons, responsive design can be used for mobile compatibility, and WebView wrappers can provide a native feel for mobile apps. However, the trade-offs for simplicity should be carefully considered against the specific requirements of the project.
   #MonolithApproach #MonolithTradeoffs

4. Q: How does the microservices architecture differ from the monolithic approach, and what are its advantages?
   A: The microservices architecture separates the system into distinct services: Frontend, Backend, SQL, Object Store, and Authentication. This differs from the monolithic approach by decoupling components, allowing for:

   1. Greater scalability: Each service can be scaled independently.
   2. Flexibility: Easier to update or replace individual components.
   3. Technology diversity: Different services can use different technologies.
   4. Easier maintenance: Smaller, focused codebases for each service.
   5. Better support for mobile apps and diverse clients.

   However, it's more complex to implement and manage, requiring careful consideration of data flow and inter-service communication.
   #MicroservicesApproach #MicroservicesComponents

5. Q: Explain the two approaches for handling image uploads in the microservices architecture and their trade-offs.
   A: The two approaches for image uploads are:

   1. Client-side uploads:

      - Pros: Reduced backend load, lower latency
      - Cons: Potential consistency issues, more complex client implementation

   2. Server-side uploads:
      - Pros: Guaranteed consistency, simplified security model
      - Cons: Increased backend load, higher latency

   The choice between these approaches depends on the specific requirements of the system, balancing factors like consistency, performance, and security.
   #DataFlowConsiderations #TradeoffAnalysis

6. Q: What strategies can be employed to scale the system as user traffic grows?
   A: Several strategies can be employed to scale the system:

   1. Functional Partitioning: Partition data by geographical region and use GeoDNS for routing.
   2. Caching: Implement an LRU cache using Redis for popular posts.
   3. CDN Utilization: Use for static assets and potentially for image delivery.
   4. SQL Cluster Scaling: Implement leader-follower replication for read scaling.
   5. Message Broker Integration: Use Kafka to handle write traffic spikes.
   6. NoSQL Considerations: Consider Cassandra or Kafka with HDFS for extreme write throughput.
   7. Replication: Replicate data across multiple data centers for improved availability and performance.

   These strategies work together to handle increased load, improve response times, and ensure system reliability as the user base grows.
   #ScalingStrategies #FunctionalPartitioning #CachingStrategy #CDNUtilization

7. Q: How does the system handle post expiration and removal?
   A: The system handles post expiration and removal through the following methods:

   1. Automatic Deletion: Posts are automatically deleted after 1 week.
   2. Cron Job or Airflow DAG: Implements a scheduled task for removing expired posts.
   3. Archival Strategies: Considers archiving older posts for cost savings and compliance reasons.
   4. Email Notifications: Sends renewal reminders to posters before post expiration.

   This approach ensures that the platform maintains fresh content, manages storage efficiently, and gives users the opportunity to renew their posts if desired.
   #PostRemoval #EmailService

8. Q: What advanced features could be implemented to enhance the user experience and platform functionality?
   A: Several advanced features could be implemented:

   1. Reporting System: Design a workflow for handling reported posts, including an appeal process.
   2. Analytics and Recommendations: Implement ETL jobs for generating insights and consider personalization features.
   3. A/B Testing: Gradual feature rollout capabilities for testing new features.
   4. Subscriptions and Saved Searches: Allow users to save searches and receive notifications for new matching posts.
   5. Rate Limiting: Prevent abuse by implementing rate limiting on API calls.
   6. Category Tagging: Implement a hierarchical category system for better organization and refined filtering.
   7. Local Regulations Compliance: Design the system to handle varying regulations by jurisdiction and implement configurable content filtering.

   These features would significantly enhance user engagement, improve content quality, and ensure the platform's compliance with local laws.
   #AdvancedTopics #ReportingSystem #AnalyticsAndRecommendations #CategoryTagging

9. Q: How does the system ensure data consistency and handle potential failures during data migrations?
   A: The system ensures data consistency and handles potential failures during migrations through the following steps:

   1. Disable writes during the migration process.
   2. Use scripts to transfer data.
   3. Implement checkpointing for fault tolerance, allowing the migration to resume from the last successful point in case of failure.
   4. Handle write errors gracefully to prevent data corruption.
   5. Consider connection draining for zero-downtime migrations.

   Additionally, the system design emphasizes avoiding migrations when possible by choosing appropriate data stores initially and considering future scalability needs in the initial design. This approach minimizes the need for complex and costly migrations in the future.
   #DataMigrationStrategies #MigrationChallenges

10. Q: Describe the monitoring and alerting strategy for this system. Why is it important?
    A: The monitoring and alerting strategy includes:

    1. Monitoring database health to ensure optimal performance.
    2. Tracking post metrics to understand user behavior and system usage.
    3. Analyzing search trends to improve search functionality and user experience.
    4. Implementing anomaly detection for identifying suspicious activity.

    This strategy is important because it allows the team to:

    1. Proactively identify and address potential issues before they impact users.
    2. Gain insights into system performance and user behavior.
    3. Ensure the system meets its performance and availability SLAs.
    4. Detect and respond to security threats or abusive behavior quickly.
    5. Make data-driven decisions for future improvements and optimizations.

    By implementing robust monitoring and alerting, the team can maintain a high-quality service and continuously improve the platform based on real-world usage data.
    #MonitoringAndAlerting
