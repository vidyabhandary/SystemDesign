# Chapter 7 - Design Craiglist (Summary)

1. User Stories and Requirements

#UserTypes

- Two main user types: viewers and posters
- Distinct functionality needed for each

#PosterFunctionality

- Create and delete posts
- Search own posts
- Post details: title, description, price, location, photos (up to 10, 1MB each)
- Optional video support (Future)
- Post renewal every 7 days via email notification

#ViewerFunctionality

- View all posts or search within a city (last 7 days)
- Apply filters to results
- View individual post details
- Contact poster (e.g. via email)
- Report fraudulent/misleading posts (Future)

#NonFunctionalRequirements

- Scalability: Support up to 10 million users per city
- Availability: 99.9% uptime
- Performance:
  - Near real-time post visibility
  - 1 second P99 for search and viewing
- Security: Poster authentication required

#StorageConsiderations

- Mostly for post data
- Localized storage per area
- Manual post creation limits growth rate
- Automatic deletion after 1 week
- Low overall storage needs - single host sufficient

2. API Design

#APIEndpoints

- Posts:
  - GET/DELETE /post/{id}
  - GET /post?search={search_string}
  - POST/PUT /post
- User Interaction:
  - POST /contact
  - POST /report
- Maintenance:
  - DELETE /old_posts

#Filters

- Implement on frontend and backend
- Examples: Neighborhood, Price range, Item condition

3. Database Schema

#SQLTables

- User: id, first_name, last_name, signup_ts
- Post: id, created_at, poster_id, location_id, title, description, price, condition, address details, contact info
- Images: id, ts, post_id, image_address
- Report: id, ts, post_id, user_id, abuse_type, message (Future)

#ImageStorage

- Use object store (e.g. AWS S3, Azure Blob Storage) for reliability and cost-efficiency
- Store image_address in database to reference object store location

4. High-Level Architecture Options

#MonolithApproach

- Single service handling UI and backend
- Store complete webpages in object store
- Pros: Simple to implement and maintain
- Cons: Limited flexibility, potential duplication

#MonolithComponents

- Single service handling UI and backend logic
- Object store for complete webpage storage
- User authentication service

#MicroservicesApproach

- Separate services: Frontend, Backend, SQL, Object Store, Authentication
- More complex but offers greater scalability and flexibility

5. #MonolithTradeoffs

- Pros:
  - Simple implementation and maintenance
  - Reduced network overhead
- Cons:
  - Limited flexibility for mobile apps
  - Potential content duplication
  - Challenges with analytics on post data

6. Microservices Architecture Deep Dive

#MicroservicesComponents

- Frontend service (UI)
- Backend service (business logic)
- SQL service (structured data)
- Object store (images)
- User authentication service

#DataFlowConsiderations

- Two approaches for image uploads:
  1. Client-side: Reduced backend load, potential consistency issues
  2. Server-side: Guaranteed consistency, increased backend load

#TradeoffAnalysis

- Client-side uploads:
  - Lower resource usage
  - Reduced latency
  * Potential inconsistencies
- Server-side uploads:
  - Guaranteed consistency
  - Simplified security model
  * Increased backend load
  * Higher latency

7. Read/Write Operations

#WritingPosts

- Client sends post data to backend
- Backend writes to SQL database
- Image upload options:
  1. Client uploads directly to object store
  2. Backend handles image uploads

#ReadingPosts

- Backend fetches post data from SQL database
- Client retrieves images from object store
- Consider parallel image downloads for performance

8. Scaling Strategies

#FunctionalPartitioning

- Partition by geographical region (e.g. city)
- Use geolocation routing (GeoDNS) to direct users
- Replicate data across multiple data centers

#CachingStrategy

- Implement LRU cache using Redis
- Cache popular posts to meet latency SLAs
- Handle cache invalidation for post updates

#CDNUtilization

- Use for static assets (CSS, JavaScript)
- Consider for image delivery if high traffic

9. Additional System Components

#EmailService

- Integrate with shared email service for notifications
- Implement batch job for renewal reminders

#SearchFunctionality

- Create Elasticsearch index on Post table
- Allow filtering before and after search

#PostRemoval

- Implement cron job or Airflow DAG for removing expired posts
- Consider archival strategies for cost savings and compliance

#MonitoringAndAlerting

- Monitor database health, post metrics, search trends
- Implement anomaly detection for suspicious activity

10. Advanced Topics

#ReportingSystem

- Design workflow for handling reported posts
- Consider appeal process and admin tools

#AnalyticsAndRecommendations

- Create ETL jobs for generating insights

#SubscriptionsAndSavedSearches

- Design system for user-defined saved searches
- Implement notification service for new matching posts

#RateLimiting

- Add rate limiting layer to prevent abuse

#LocalRegulations

- Design system to handle varying regulations by jurisdiction
- Implement configurable content filtering mechanisms

---

References:

1. [Run ExecuteNonQuery, ExecuteReader, and ExecuteScalar Operations using the SQL adapter](https://learn.microsoft.com/en-us/biztalk/adapters-and-accelerators/adapter-sql/executenonquery-executereader-and-executescalar-using-the-sql-adapter)

ExecuteNonQuery: Use this operation to execute any arbitrary SQL statements in SQL Server if you do not want any result set to be returned. You can use this operation to create database objects or change data in a database by executing UPDATE, INSERT, or DELETE statements.
