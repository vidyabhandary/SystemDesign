# Chapter 13 - Design a Content Delivery Network - (Q&A)

## 1. Advantages and Disadvantages of a CDN

### **Advantages of Using a CDN**

**Q1: How does using a CDN reduce latency for users?**  
_A_: A CDN serves content from data centers geographically closer to users, minimizing delays. This improves user experience and indirectly boosts SEO by reducing bounce rates caused by slow load times.  
#LatencyReduction #SEOImprovement  

**Q2: Why is scalability considered an advantage when using a third-party CDN?**  
_A_: Third-party CDNs handle scaling automatically, eliminating the need for companies to manage infrastructure growth themselves, thus simplifying operations.  
#Scalability #ThirdPartyManagement  

**Q3: Explain how CDNs achieve lower unit costs as traffic increases.**  
_A_: CDNs leverage economies of scale by serving multiple clients, spreading hardware and operational costs over a larger volume of traffic. Bulk discounts further reduce per-unit costs.  
#CostEfficiency #EconomiesOfScale  

**Q4: How does a CDN improve throughput for services?**  
_A_: By adding more hosts across distributed data centers, CDNs can handle a higher number of simultaneous users and increased traffic loads.  
#ThroughputIncrease #HighTrafficHandling  

**Q5: In what ways does a CDN enhance system availability?**  
_A_: Geographically distributed data centers act as fallbacks if one fails, ensuring continuous service even during regional outages or unexpected traffic spikes.  
#HighAvailability #FaultTolerance  

---

### **2. Disadvantages of Using a CDN**

**Q6: What complexities arise from integrating a CDN into an existing system?**  
_A_: Additional DNS lookups and potential points of failure are introduced, requiring careful monitoring and management.  
#IntegrationComplexity #PotentialFailures  

**Q7: Why might CDNs be costly for low-traffic scenarios?**  
_A_: Hidden costs, such as per-GB data transfer fees, can make CDNs expensive for applications with minimal traffic.  
#CostAnalysis #LowTrafficConsiderations  

**Q8: Describe challenges associated with migrating to a different CDN provider.**  
_A_: Migration can be time-consuming and costly, especially if the current CDN lacks coverage in certain regions or provides poor service.  
#ProviderMigration #CostlyTransitions  

**Q9: How do geopolitical restrictions impact CDN usage?**  
_A_: Some countries may block specific CDN IP addresses, limiting access for users in those regions.  
#GeopoliticalRestrictions #IPBlocking  

**Q10: What security concerns arise from storing data on a third-party CDN?**  
_A_: Data privacy risks emerge, and encryption adds overhead. Ensuring secure key management requires expertise and additional resources.  
#DataSecurity #EncryptionOverhead  

## 3. Requirements

**Q11: What are the functional requirements for a CDN?**  
_A_: Users should be able to create directories, upload files up to ~10 GB, and download files securely.  
#FunctionalRequirements #FileManagement  

**Q12: List the non-functional requirements for a CDN and explain their importance.**  
_A_: Scalability ensures handling large volumes; high availability guarantees uptime; performance minimizes latency; durability prevents data corruption; and security ensures only authorized access.  
#NonFunctionalRequirements #SystemDesign  

---

## 4. CDN Authentication and Authorization

**Q13: Differentiate between authentication and authorization in the context of a CDN.**  
_A_: Authentication verifies a user’s identity, while authorization ensures they have permission to access specific resources, preventing unauthorized access.  
#Authentication #Authorization  

**Q14: Why is token-based authentication preferred over cookie-based methods?**  
_A_: Token-based systems use less memory, integrate better with third-party services, and allow granular access control, including IP restrictions.  
#TokenAuth #GranularControl  

**Q15: What is key rotation, and why is it important?**  
_A_: Key rotation periodically updates secret keys to limit damage from breaches, ensuring both old and new keys remain valid during the transition period.  
#KeyRotation #SecurityUpdates  

---

## 5. High-Level Architecture

**Q16: What role does an API gateway play in a CDN’s architecture?**  
_A_: It manages SSL termination, authentication, rate limiting, logging, and metadata lookups to determine optimal storage hosts for requests.  
#APIGateway #CentralizedControl  

**Q17: How does a metadata service contribute to a CDN’s functionality?**  
_A_: It tracks file locations, health statuses, and encryption details, enabling efficient resource allocation and retrieval.  
#MetadataService #ResourceAllocation  

---
## 6. Common Operations

**Q18: Outline the steps involved in downloading a file from a CDN.**  
_A_: Check rate limits, query metadata for storage hosts, select a host, stream the file, and update metadata with load changes.  
#DownloadProcess #LoadBalancing  

**Q19: How are large files typically uploaded to a CDN?**  
_A_: Using multipart uploads, files are divided into chunks, allowing retries for failed parts without restarting the entire process.  
#MultipartUploads #ChunkedTransfers  

**Q20: What measures ensure data security during file storage?**  
_A_: Encryption at rest safeguards sensitive data, managed via secrets management services to handle keys securely.  
#EncryptionAtRest #SecretsManagement  

---

## 7. Logging, Monitoring, and Alerting

**Q21: What metrics should be monitored in a CDN?**  
_A_: Track upload states, CDN misses, request rates, and unusual activity to maintain performance and security.  
#MonitoringMetrics #AlertingStrategies  

---

## 8. Other Possible Discussions

**Q22: How can media files be made playable before fully downloading?**  
_A_: Split files into smaller sequential chunks, allowing partial playback while remaining parts download in the background.  
#PartialPlayback #ProgressiveLoading  
