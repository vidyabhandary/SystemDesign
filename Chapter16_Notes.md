# Chapter 16 - Design a newsfeed (Summary)

[Mindmap for designing a newsfeed](https://raw.githubusercontent.com/vidyabhandary/SystemDesign/refs/heads/main/imgs/DesignNewsfeed.svg)

# Comprehensive Study Notes: Designing a News Feed System

## Introduction to News Feed Design

A news feed system provides users with a collection of news items sorted in approximately reverse chronological order based on topics selected by the user.

Key characteristics:

- Each news item can be categorized into 1-3 topics
- Users can select up to three topics of interest at any time
- Unlike social media feeds (Facebook/Twitter) which show posts from connections, this news feed shows posts from general contributors
- Items are sorted in reverse chronological order (newest first)

#newsfeed #system-design #content-delivery #user-personalization

## System Requirements

### Functional Requirements

1. **Topic Selection**

   - Users can select topics of interest (up to 100 tags)
   - System uses "tags" instead of "news topics" to avoid confusion with Kafka topics

2. **Content Retrieval**

   - Users can fetch English-language news items 10 at a time
   - Maximum of 1,000 items can be retrieved at once
   - System archives all items (unlimited historical storage)

3. **Content Display**

   - Items arranged in reverse chronological order (approximate)
   - Initially location-agnostic (same content regardless of user location)

4. **News Item Structure**

   - Text field with 10,000-character limit
   - UNIX timestamp for creation time
   - Initially text-only, with potential expansion to include images (0-10 images up to 1MB each)

5. **Content Moderation**

   - System must filter inappropriate content

#functional-requirements #content-structure #retrieval-limits #moderation

### Out-of-Scope Features

- Versioning (multiple article versions, edits, corrections)
- Advanced analytics on user data
- Sophisticated recommendation systems
- Social features (sharing, commenting)
- News source verification (simple POST API for adding items)
- Search functionality
- Monetization features (login, payments, subscriptions, ads)

#scope-limitations #excluded-features

### Non-Functional Requirements

1. **Scalability**

   - Support for 100K daily active users (10 requests/day per user)
   - Capacity for one million news items per day

2. **Performance**

   - One-second P99 latency for reads

3. **Privacy**

   - User data protection

4. **Consistency**

   - Eventual consistency acceptable (up to a few hours)
   - Ideally serve new content within seconds

5. **Availability**

   - High availability required for writes
   - High availability for reads desirable but not required
   - Users can cache old news on devices

#non-functional-requirements #scalability #performance #privacy #consistency #availability

## High-Level Architecture Design

### Initial Architecture Overview

1. **Components Flow**:

   - News sources → Ingestion Service → Database
   - Users ← News Feed Service ← Database

2. **Key Architecture Observations**:

   - Ingestion service must handle unpredictable, heavy traffic
   - Consider Kafka as an event streaming platform
   - Database strategy:
     - Archive database for all historical items
     - Serving database for the 1,000 most recent items per tag
     - Data size estimation: 1,000 items × 100 tags × 40KB = ~4GB (fits in Redis)
     - HDFS for archival storage

#architecture-overview #data-flow #database-strategy

### Detailed Architecture Components

1. **Client/Source**: Submits news posts to the system
2. **Ingestion Service**:

   - Performs validation on individual posts (no dependencies on other data)
   - Validation types:
     - SQL injection prevention
     - Inappropriate content filtering
     - Source/user blocking verification
     - Field length checks
     - Character validation
   - Pushes valid posts to Kafka queue
   - Returns appropriate status codes (200 Success or 400 Bad Request)

3. **Queue System**:

   - Kafka queues for handling unpredictable traffic
   - Decouples ingestion from processing

4. **Consumer Service**:

   - Polls from Kafka queue
   - Writes to HDFS storage
   - Tables partitioned by tag and hour

5. **HDFS Storage**:

   - Raw news items table
   - Ready-to-serve news items table
   - Items requiring manual review table

6. **ETL Jobs**:

   - Perform validation tasks dependent on other data
   - Run parallel validation processes
   - Find duplicate items
   - Check rate limits for tags/subjects
   - Compute intersection of valid IDs
   - Update Redis cache with approved content

7. **API Gateway**:

   - Handles user GET /post requests
   - Manages authentication, authorization, rate limiting
   - Queries metadata service for user's tags
   - Retrieves appropriate news items via backend service

8. **Backend Service**:

   - Interfaces with Redis cache
   - Updates Redis when consuming from queue
   - Serves content to users via API Gateway

9. **Redis Cache**:

   - Stores recent news items for quick retrieval
   - Each post contains ID and content

10. **Metadata Service**:

    - Stores user tag preferences

11. **Notification Service**:

    - Sends notifications via multiple channels
    - Mobile/browser apps, email, texting, social media

12. **Moderation Service**:

    - Centralizes content moderation logic

#architecture-components #ingestion #validation #queuing #storage #processing #serving

### Request Flow and Processing

1. **Post Submission Flow**:

   - Source submits post → Ingestor validates
   - If valid: Ingestor → Kafka queue → Consumer → HDFS
   - ETL jobs process posts → Updates Redis/sends notifications

2. **Content Retrieval Flow**:

   - User requests content → API Gateway
   - Gateway gets user tags from Metadata Service
   - Gateway queries backend for relevant content
   - Backend retrieves from Redis → Returns to user

3. **Update Frequency**:

   - Device may limit update frequency (e.g., hourly)
   - Reduces service load
   - Options:
     - Device ignores frequent requests
     - No retries on timeouts

#request-flow #submission-process #retrieval-process #update-frequency

### Storage Optimization Strategies

1. **Mobile-Only Client Approach**:

   - Store posts on client device
   - Delete server-side rows after fetch
   - Acceptable for ephemeral news content

2. **Time-Based Cleanup**:

   - Add timestamp column
   - ETL job periodically deletes old rows (>24 hours)

3. **Hybrid Approach**:

   - Use prepared feed for first request
   - Serve additional requests from Redis
   - Reduces storage requirements while maintaining performance

4. **Client-Side Deduplication**:

   - Option 1: Client includes already-fetched post IDs in request
   - Option 2: Client requests posts by time increments
   - Option 3: Separate endpoint for fetching IDs vs. content

#storage-optimization #client-side-storage #deduplication #hybrid-approach

## Content Validation and Moderation

### Content Filtering Process

1. **Validation Challenges**:

   - Some issues hard to automate (e.g., truncated posts)
   - Complex inappropriate content detection
   - Regional/demographic content restrictions

2. **Post-Delivery Content Management**:

   - Mechanism to remove/update incorrect posts
   - Modified GET /posts endpoint returns:
     - List of corrected posts
     - List of posts to be deleted
   - Post events: REPLACE or DELETE
   - Client-side implementation of corrections

### Moderation Service Architecture

1. **Validation Points**:

   - Client-side
   - Ingestor
   - ETL jobs
   - Backend (during requests)

2. **Consolidated Moderation Service**:

   - Single service for all moderation logic
   - Admin features:
     - Configure validation tasks and filters
     - Execute post change/delete decisions
   - Benefits:
     - No duplicate validations
     - Non-technical staff can perform moderation
     - Logging for audits/reviews/rollbacks

3. **Moderation Request Flow**:

   - Similar to other write requests
   - Produces to news feed topic
   - News feed service consumes event
   - Updates Redis data

#moderation-service #validation-points #admin-features #request-flow

## System Monitoring and Media Support

### Logging, Monitoring, and Alerting

**Key Monitoring Areas**:

1. Unusual traffic rates from sources
2. High validation failure rates
3. Negative user reactions (flags, reports)
4. Processing time anomalies in pipeline

#monitoring #alerting #anomaly-detection

### Adding Media Support

1. **Image Support Considerations**:

   - 0-10 images per post (up to 1MB each)
   - Increased GET request overhead
   - Image file characteristics:
     - Larger than text content
     - Potential reuse across posts
     - Different validation requirements

2. **Architecture with Media Service**:

   - Synchronous media upload process
   - Larger ingestor cluster required
   - Media stored on shared object service
   - Replicated across data centers

3. **Upload Process Flow**:

   - Complete media uploads before queue production
   - Hash-based deduplication
   - Error handling for partial failures
   - Periodic audit jobs for orphaned media

4. **CDN Integration**:

   - Geographical distribution support
   - User traffic handling
   - Media service writes to CDN
   - Users download directly from CDN
   - Cost reduction by only storing approved media

5. #media-support #cdn-integration #upload-process #review-optimization

## Design Summary

**Key Design Principles**:

1. Start with core data flows and storage needs
2. Select appropriate databases based on non-functional requirements
3. Separate latency-sensitive operations from batch processing
4. Wrap processing operations in dedicated services
5. Implement comprehensive logging and monitoring

This design balances performance, scalability, and maintainability while allowing for future expansion to handle media content and additional features.

#design-principles #architecture-summary

References:

1. [What is DAG?](https://www.youtube.com/watch?v=1Yh5S-S6wsI)

2. [What's a DAG, and Why Should I Care?](https://www.youtube.com/watch?v=IltIGJOHYgo)

3. [Sharding](https://www.youtube.com/watch?v=0DzRdQ-sOTg)

4. [Database Replication & Sharding Explained](https://www.youtube.com/watch?v=jLEp1XI_L6Q)

5. [Partitioning Vs Sharding](https://github.com/vidyabhandary/til/blob/master/misc/PartioningVsSharding.md)

6. [Consistent Hashing](https://github.com/vidyabhandary/til/blob/master/misc/ConsistentHashing.md)

7. [Consistent Hashing | Algorithms You Should Know #1](https://www.youtube.com/watch?v=UF9Iqmg94tk)

8.[What is Consistent Hashing and Where is it used?](https://www.youtube.com/watch?v=zaRkONvyGr8)
