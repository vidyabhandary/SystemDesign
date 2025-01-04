# Chapter 12 - Design Flickr - (Q&A)

#### **Architectural Insights and Design Decisions**

**Q1:** What are the primary components of the system's architecture?  
**A1:** The architecture consists of a backend service for processing requests, an SQL service for metadata storage, a file storage service for managing image uploads, and a CDN for global, fast delivery of images. These components ensure scalability and reliability.

- #ArchitectureComponents #Scalability

**Q2:** How does the system ensure efficient access to image files?  
**A2:** A CDN is used to cache and distribute image files globally, reducing latency. The file storage service acts as a buffer for uploads and backups, while SQL stores metadata like access permissions and image details.

- #CDN #EfficientAccess

**Q3:** How are thumbnails generated, and what are the trade-offs of different approaches?  
**A3:** Thumbnails can be generated either on the client-side or server-side. Client-side generation reduces backend load but can be inconsistent due to device variations. Server-side generation is centralized and reliable but demands more server resources.

- #ThumbnailGeneration #Tradeoffs

**Q4:** What techniques are used for organizing and accessing image files on the CDN?  
**A4:** Files are organized hierarchically: User Directory → Album → Resolution → File. This logical structure enables efficient access and management. Unsorted images are placed in a default album.

- #FileOrganization #CDNHierarchy

**Q5:** How does the system balance eventual consistency and real-time requirements?  
**A5:** Eventual consistency is used for most data, such as photos and comments, to reduce latency and costs. However, stricter consistency is applied to privacy settings and access control to ensure timely enforcement.

- #ConsistencyTradeoffs #DataConsistency

---

#### **Scalability and Performance**

**Q6:** What scalability considerations were made for this system?  
**A6:** The system is designed to handle 1 billion users, 1 PB of daily uploads, and traffic spikes up to 10 GB/sec. Components like CDN caching and asynchronous workflows ensure smooth scalability under these conditions.

- #Scalability #HighDemand

**Q7:** How is high availability ensured in the system?  
**A7:** By targeting 99.999% uptime (five 9s), the system employs redundant storage, robust monitoring, and fault-tolerant components to minimize downtime.

- #HighAvailability #FaultTolerance

**Q8:** What role do saga patterns play in the system?  
**A8:** Saga patterns, either choreography or orchestration, manage asynchronous uploads and thumbnail generation. They ensure fault tolerance by breaking workflows into manageable steps with compensatory actions.

- #SagaPattern #AsynchronousProcessing

**Q9:** How does the system optimize performance for thumbnails?  
**A9:** Thumbnails are pre-generated and cached for quick access. P99 latency is kept under 1 second to ensure high performance. Popular images are cached in both Redis and client devices for faster retrieval.

- #ThumbnailPerformance #Caching

---

#### **Data Management and Security**

**Q10:** How does the SQL schema support system requirements?  
**A10:** The schema includes tables for images, directories, sharing permissions, and favorites. Composite indexes (e.g., resolution + user_id) enhance query speed, and metadata is optimized for frequent access.

- #SQLSchema #DataOptimization

**Q11:** What mechanisms secure user data and image files?  
**A11:** OAuth2 tokens authorize secure downloads from the CDN. The backend and storage services enforce strict access controls to prevent unauthorized data access.

- #DataSecurity #AccessControl

**Q12:** How is metadata used in the system?  
**A12:** Metadata is crucial for organizing, searching, and managing photos. It includes predefined fields (tags, locations) and dynamic fields (viewer access, timestamps), ensuring efficient functionality.

- #MetadataManagement #Searchability

**Q13:** How is redundancy managed in storage systems?  
**A13:** The file storage service and CDN act as backups for each other. The system retains files temporarily in the file storage service to handle failures or slow CDN replication.

- #Redundancy #BackupStrategies

---

#### **Monitoring and Enhancements**

**Q14:** What monitoring mechanisms are implemented?  
**A14:** Monitoring tracks file uploads/downloads, SQL queries, and caching performance. Alerts are set up for failures or performance degradation to maintain system reliability.

- #Monitoring #Alerting

**Q15:** What future enhancements are planned for the system?  
**A15:** Potential upgrades include watermarking for copyright protection, advanced analytics pipelines, and support for video and audio streaming to expand service offerings.

- #FutureEnhancements #Innovation
