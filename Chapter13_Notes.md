# Chapter 13 - Design a Content Delivery Network (Summary)

[Mindmap for designing a Content Delivery Network](https://raw.githubusercontent.com/vidyabhandary/SystemDesign/99d715e8b0e0aa2e71609d0f816a8460a176bc8f/imgs/DesignCDN.svg)

## 13.1 Advantages and Disadvantages of a CDN

### 13.1.1 Advantages of Using a CDN

- **Reduced Latency**: Users are served from nearby data centers (mostly static content), minimizing delays. This also improves SEO as search engines favor faster websites.

  - _Understanding_: Faster load times enhance user experience and reduce bounce rates.
  - #LatencyReduction #SEOImprovement

- **Scalability**: Third-party providers handle scaling, removing the complexity from your team.

  - _Understanding_: Offloading scalability concerns allows focus on core services.
    #Scalability #ThirdPartyManagement

- **Cost Efficiency**: Bulk discounts lead to lower costs per unit as traffic increases.

  - _Understanding_: Economies of scale benefit from shared infrastructure across multiple clients.
    #CostEfficiency #EconomiesOfScale

- **Increased Throughput**: Additional hosts allow serving more users simultaneously.

  - _Understanding_: More servers mean more capacity to handle high traffic volumes.
    #ThroughputIncrease #HighTrafficHandling

- **Enhanced Availability**: Geographically distributed data centers ensure service continuity even if one fails.
  - _Understanding_: Redundancy ensures uninterrupted service during failures or spikes in demand.
    #HighAvailability #FaultTolerance

### 13.1.2 Disadvantages of Using a CDN

- **Complexity**: Integrating another service adds layers like DNS lookups and potential points of failure.

  - _Understanding_: Managing dependencies requires careful planning and monitoring.
    #IntegrationComplexity #PotentialFailures

- **High Costs for Low Traffic**: Initial costs might be prohibitive for low-traffic scenarios.

  - _Understanding_: Evaluate cost-benefit analysis before committing to a CDN.
    #CostAnalysis #LowTrafficConsiderations

- **Migration Challenges**: Switching providers can be costly and time-consuming.

  - _Understanding_: Plan migrations carefully to avoid disruptions.
    #ProviderMigration #CostlyTransitions

- **Geopolitical Restrictions**: Certain regions may block specific CDN IP addresses.

  - _Understanding_: Stay informed about regional restrictions when choosing a provider.
    #GeopoliticalRestrictions #IPBlocking

- **Security Concerns**: Storing data externally poses risks; encryption mitigates but adds overhead.
  - _Understanding_: Balance security needs with performance impacts.
    #DataSecurity #EncryptionOverhead

## 13.2 Requirements

- **Functional Needs**: Users should manage directories and files up to say ~10 GB.

  - _Understanding_: Basic CRUD operations form the foundation of functionality.
    #CRUDOperations #FileManagement

- **Non-Functional Needs**: Focus on scalability, availability, performance, durability, and security.
  - _Understanding_: These attributes define the quality of service.
    #Scalability #Availability #Performance #Durability #Security

## 13.3 CDN Authentication and Authorization

- **Authentication vs. Authorization**: Verify identity versus granting access rights.

  - _Understanding_: Both are critical for secure access control.
    #Authentication #Authorization

- **Token-Based Auth**: Preferred over cookies due to efficiency and granular control.

  - _Understanding_: Tokens offer better security features and integration capabilities.
    #TokenAuth #GranularControl

- **Access Token Generation**: Involves secret keys, URLs, expiry times, referrers, IPs, and regions.

  - _Understanding_: Detailed token generation ensures precise access management.
    #AccessTokenGeneration #DetailedAccessManagement

- **Key Rotation**: Periodic key changes limit damage from breaches.
  - _Understanding_: Regular updates maintain long-term security.
    #KeyRotation #SecurityUpdates

## 13.4 High-Level Architecture

- **API Gateway Role**: Manages requests, SSL termination, auth, rate limiting, and logging.

  - _Understanding_: Centralized control simplifies complex workflows.
    #APIGateway #CentralizedControl

- **Metadata Service**: Tracks storage locations, file details, and health statuses.
  - _Understanding_: Metadata is vital for efficient resource allocation.
    #MetadataService #ResourceAllocation

## 13.5 Storage Service

- **Cluster Structure**: Hosts/nodes store files with replication for reliability.

  - _Understanding_: Distributed storage enhances fault tolerance.
    #ClusterStructure #FaultTolerance

- **In-Cluster Management**: Tools like HDFS and ZooKeeper manage internal roles.

  - _Understanding_: Sophisticated components require reliable management.
    #InClusterManagement #ReliableComponents

- **Out-Cluster Management**: Independent clusters handle node assignments.
  - _Understanding_: Decoupled management reduces single points of failure.
    #OutClusterManagement #DecoupledSystems

## 13.6 Common Operations

- **Reads (Downloads)**: Select optimal storage hosts using metadata.

  - _Understanding_: Efficient routing minimizes latency.
    #OptimalRouting #LatencyMinimization

- **Writes (Uploads/Deletions)**: Handle large files via multipart uploads.

  - _Understanding_: Chunked transfers improve reliability.
    #MultipartUploads #ChunkedTransfers

- **Encryption at Rest**: Use secrets management for secure key handling.
  - _Understanding_: Encryption safeguards sensitive data.
    #EncryptionAtRest #SecretsManagement

## 13.7 Cache Invalidation

- **Fingerprinting Files**: Unique identifiers prevent stale cache issues.
- Stale file monitoring
- _Understanding_: Versioning ensures freshness.
  #CacheInvalidation #Versioning

## 13.8 Logging, Monitoring, and Alerting

- **Tracking States**: Monitor upload progress and outcomes, CDN misses tracking, request rate logging, usage patterns, geographic distribution.

  - _Understanding_: Shared logging service.
    #StateTracking #TroubleshootingAids

- **Unusual Activity Detection**: Alerts flag suspicious behaviors.
  - _Understanding_: Proactive measures enhance security.
    #AnomalyDetection #ProactiveSecurity

## 13.9 Other items

- **Partial Playback**: Sequential downloads enable early playback.
  - _Understanding_: Progressive loading improves user experience.
    #PartialPlayback #ProgressiveLoading
    _Understanding_: Progressive loading improves user experience.

---

References:

1. [What is a CDN?](https://www.youtube.com/watch?v=RI9np1LWzqw)

2. [How does DNS work?](https://www.youtube.com/watch?v=27r4Bzuj5NQ)

3. [Hoes does live streaming work?](https://www.youtube.com/watch?v=7AMRfNKwuYo)
