# Chapter 16 - Design a Newsfeed - (Q&A)

## Question 1

What are the main functional requirements for the news feed system? #SystemDesign #Requirements #NewsFeed

**Answer:** The main functional requirements for the news feed system include:

- Users can select up to 3 topics of interest from 100 available tags
- Users can fetch English-language news items 10 at a time, up to 1,000 items
- All news items should be archived in the system
- News items should be displayed in approximate reverse chronological order
- News items typically contain text fields (up to 10,000 characters) and a timestamp
- The system should filter out inappropriate content
- The system should eventually support 0-10 image files of up to 1MB each per news item
- The system should initially prioritize serving text content before handling more complex media types

## Question 2

How does the high-level architecture handle the ingestion of news items? #Architecture #Ingestion #Kafka #EventStreaming

**Answer:** The high-level architecture handles news item ingestion through several components:

1. Sources submit news items to an ingestion service
2. The ingestor performs validation tasks like sanitizing values, filtering inappropriate language, checking required fields, and verifying character limits
3. For valid items, the ingestor produces the item to a Kafka queue and returns a 200 Success response to the source
4. For invalid items, the ingestor returns a 400 Bad Request response with failed validation details
5. A consumer cluster polls from the Kafka queue and writes the news items to HDFS
6. HDFS tables are partitioned by tag and hour
7. ETL jobs process the raw news items for further validation before serving them to users

This approach provides high availability for writes, handles unpredictable traffic volumes, and ensures consistent validation before items reach users.

## Question 3

Why does the system use both HDFS and Redis? #DataStorage #CQRS #CDC #DataFlow

**Answer:** The system uses both HDFS and Redis for different purposes:

HDFS is used for:

- Archiving all news items (potentially billions)
- Storing raw news items submitted by the consumer
- Maintaining items ready to be served to users
- Storing items that require manual review

Redis is used for:

- Serving the actively requested news items (only ~1000 items per tag)
- Providing fast read performance for user requests

## Question 4

How does the news feed system handle content moderation? #ContentModeration #Filtering #Validation #ApprovalService

**Answer:** The news feed system handles content moderation through multiple layers:

1. Client-side validation: Initial checks on user devices before submission
2. Ingestor validation: Server-side validation when posts are first received
   - Sanitizes values to prevent SQL injection
   - Detects inappropriate language
   - Checks if posts are from blocked sources
   - Validates required fields and character limits
3. ETL job validation: More complex checks that depend on other items or data
   - Finding duplicate items
   - Enforcing limits on topics within time periods
4. Moderation service: A centralized service that:
   - Provides interfaces for admins to configure validation tasks and filters
   - Executes moderation decisions to change or delete posts
   - Logs decisions for reviews, audits, or rollbacks
5. Approval service: For manual review of flagged content
   - Receives posts flagged by ETL jobs
   - Allows human reviewers to approve or reject content
   - Approved content is sent to the Kafka queue for serving
   - Rejected content notifications are sent to the source

The system also uses tagging with "filters" that allow for region-specific or demographic-specific content moderation, ensuring users don't see content inappropriate for their location or age group.

## Question 5

What approach does the system use to optimize news feed delivery, and what are the tradeoffs? #Performance #PreparedFeeds #Optimization #Tradeoffs

**Answer:** The system optimizes news feed delivery by preparing feeds in advance, which trades higher storage for lower latency and reduced traffic. This involves:

Tradeoffs include:

- Advantages:
  - Reduces read traffic on the news feed service
  - Lowers latency for initial feed load
  - Avoids multiple Redis queries per user request
  - Allows for personalization of content
- Challenges:
  - Storage requirements could be enormous
  - Requires complex sharding and data distribution strategies
  - Adds complexity to the system architecture

## Question 6

How does the system handle media content like images alongside text posts? #MediaHandling #CDN #ImageStorage #Scalability

**Answer:** The system handles media content through a dedicated media service and CDN integration:

1. A media service is added to manage image uploads separately from text content:

   - Sources upload media to the media service first
   - The media service stores media on a shared object service
   - Media is replicated across data centers for global access
   - The media service can hash files to detect duplicates and avoid redundant transfers

2. For widely distributed users or heavy media traffic, a CDN is employed:

   - The media service writes approved media to the CDN
   - Users download images directly from the CDN
   - ETL jobs and approval services coordinate with the media service
   - Authorization tokens for CDN downloads are managed by the API gateway

3. This approach addresses several challenges:

   - Images (up to 10MB) are much larger than text (40KB), requiring different storage considerations
   - Media files may be reused across posts
   - Validation algorithms for images require different processing libraries
   - Media uploads are more resource-intensive and time-consuming

This separation of concerns allows the system to scale media handling independently from text processing while maintaining performance.

## Question 7

What monitoring and alerting mechanisms should be implemented for the news feed system? #Monitoring #Alerting #Logging #SystemHealth

**Answer:** The news feed system should implement comprehensive monitoring and alerting mechanisms including:

1. Standard system monitoring as discussed in section 2.5:

   - Resource utilization (CPU, memory, disk, network)
   - Service response times and error rates
   - Database performance metrics
   - Queue depths and processing rates

2. News feed specific metrics:

   - Unusually large or small traffic rates from particular sources
   - Abnormal rates of items failing validation (both overall and per source)
   - Negative user reactions (like flagging articles for abuse or errors)
   - Processing time across the pipeline (comparing item upload timestamp to Redis availability)

3. Pipeline performance monitoring:

   - Pipeline component processing times to identify bottlenecks
   - Scaling indicators for components needing additional resources
   - Inefficient pipeline operations that need optimization

4. Content delivery monitoring:

   - CDN performance and availability for media content
   - Media service upload and processing times
   - Image and text delivery latencies

5. User experience metrics:

   - Feed loading times
   - User engagement with content
   - Content refresh rates

These mechanisms help ensure the system meets its non-functional requirements like one-second P99 for reads, availability targets, and scalability needs. They also enable early detection of issues that could affect user experience or system performance, allowing for proactive intervention.

## Question 8

What is the role of ETL jobs in the news feed architecture ? #ETL #DataProcessing #EventProcessing

**Answer:** ETL (Extract, Transform, Load) jobs play a crucial role in the news feed architecture:
Role of ETL jobs:

1. Data validation - Running complex validation tasks that depend on other news items or external data:

   - Finding duplicate items
   - Enforcing limits on topics within time periods
   - Performing content moderation checks

2. Data transformation - Processing raw news items into formats optimized for serving:

   - Creating intermediate HDFS tables with valid post IDs
   - Determining intersections of valid post sets
   - Preparing data for Redis caching

3. System integration - Connecting various components:

   - Writing processed data to another Kafka queue
   - Triggering notifications via notification services
   - Informing the media service about approved content

4. Feed preparation - Creating optimized data structures:

   - Preparing user feeds in advance
   - Building the Redis tables with appropriate keys and values
   - Updating feeds with new content

## Question 9

How would you explain the non-functional requirements of the news feed system and their impact on the architecture? #NonFunctionalRequirements #Performance #Scalability #Consistency

**Answer:** The non-functional requirements of the news feed system significantly influence the architecture in several ways:

1. Scalability (100K daily active users, 10 requests per user, 1M news items/day):

   - Impact: Requires distributed components, horizontal scaling, and efficient data storage
   - Architecture elements: Kafka for handling unpredictable traffic, sharded SQL for user data, HDFS for archival, Redis for serving active content
   - The system can scale different components independently based on their specific load patterns

2. Performance (one-second P99 for reads):

   - Impact: Necessitates low-latency data access and efficient query processing
   - Architecture elements: Redis caching for fast reads, prepared feeds strategy to reduce query time, CDN for media delivery
   - The separation between archival storage and serving cache optimizes read performance

3. Privacy (user data is private):

   - Impact: Requires secure data handling and appropriate access controls
   - Architecture elements: Authentication and authorization services, proper filtering of content based on user attributes
   - Content moderation features ensure appropriate content delivery

4. Consistency (eventual consistency up to a few hours):

   - Impact: Allows for batch processing and asynchronous updates between data stores
   - Architecture elements: ETL jobs that run periodically, Kafka queues for reliable message delivery
   - The system can prioritize availability and partition tolerance over strict consistency
   - Client-side caching is possible since immediate updates aren't required

These non-functional requirements led to several key architectural decisions:

- Separation of concerns: Different components handle ingestion, processing, storage, and serving
- Multiple data stores: HDFS for archival, Redis for serving, SQL for user-post mappings
- Asynchronous processing: ETL jobs handle complex validations without blocking user interactions
- Caching strategies: Prepared feeds and Redis caching to meet performance requirements
- Content distribution: CDN integration for efficient media delivery
- Sharding and replication: For horizontal scaling and fault tolerance

The architecture balances these requirements through a combination of techniques from distributed systems, ensuring the system can scale to the required load while maintaining performance, availability, and adequate consistency guarantees.

## Question 10

What are the key differences between tags and filters in the news feed system? How does each affect content delivery? #ContentDelivery #Tags #Filters #UserExperience

**Answer:** Tags and filters in the news feed system serve different purposes and affect content delivery in distinct ways:

**Tags:**

1. Purpose: Represent topics of interest selected by users
2. Control: User-defined (users can select up to 3 topics from 100 available tags)
3. Configuration: Managed through user preferences in the metadata service
4. Function: Determine which content users want to see (inclusion mechanism)
5. Effect: Used to include relevant content in a user's feed
6. Query: Backend uses tags to find post IDs matching user interests

**Filters:**

1. Purpose: Represent restrictions or limitations on content visibility
2. Control: System-defined (completely controlled by the platform)
3. Configuration: Managed through the moderation service by administrators
4. Function: Determine which content users should not see (exclusion mechanism)
5. Effect: Used to exclude inappropriate content from a user's feed
6. Applications: Region-specific rules, age restrictions, content categorization

**Content Delivery Effects:**

- Tags create a personalized feed based on user preferences
- Filters implement content governance based on demographic, regional, or content type restrictions
- The combination ensures users see content they're interested in (tags) while being protected from inappropriate content (filters)
- When preparing feeds in advance, both tags and filters are considered to create appropriate user-post mappings

This dual system provides a balance between user choice and platform governance, allowing for personalization while maintaining appropriate content standards across different regions and demographics.
