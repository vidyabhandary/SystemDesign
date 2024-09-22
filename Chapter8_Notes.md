# Chapter 8 - Design a rate limiting service (Summary)

[Mindmap for Designing a rate limiting service](https://github.com/vidyabhandary/SystemDesign/blob/ffbb5850d7a5320ba7e44b5ade1f2364584b7751/imgs/DesignRateLimiter.svg)

1. Introduction to Rate Limiting

   - Purpose: Prevent service overuse and protect against malicious attacks
   - Applicability: Essential for most system designs
   - Key Concepts:
     - Defines request rate limits for API endpoints
     - Protects against inadvertent and malicious overuse
     - Crucial for maintaining service stability and performance

   #RateLimiting #APIProtection #SystemDesign

2. Types of Overuse and Attacks

   2.1 Inadvertent Overuse:

   - Traffic spikes from legitimate or malicious sources
   - Unintended load testing on production environments

     2.2 Malicious Attacks:

   - Denial-of-Service (DoS) and Distributed Denial-of-Service (DDoS)
   - Brute force attacks on sensitive data
   - Web scraping for large-scale data extraction

   #ServiceProtection #SecurityMeasures #AttackPrevention

3. Alternatives to Rate Limiting and Their Limitations

   3.1 Scaling Out:

   - Adding more hosts during load spikes
   - Limitations: Slow provisioning, potential service crashes

     3.2 Load Balancer Approach:

   - Using load balancers to limit requests per host
   - Limitations: Processes malicious requests, lacks granularity

     3.3 Host-Level Tracking:

   - Individual hosts tracking request rates
   - Limitations: Doesn't account for expensive requests, requires complex load balancing

   Table: Comparison of Rate Limiting Alternatives
   | Method | Pros | Cons |
   |-----------------|---------------------------|------------------------------------------|
   | Scaling Out | Can handle traffic spikes | Slow provisioning, potential crashes |
   | Load Balancer | Simple implementation | Processes malicious requests |
   | Host-Level | Distributed approach | Lacks global view, complex load balancing |

   #ScalingStrategies #LoadBalancing #DistributedSystems

4. When Not to Use Rate Limiting

   - Scenarios where it may lead to poor user experience
   - Examples: Social media subscriptions, tiered service models
   - Alternative approaches for managing usage in these cases

   #UserExperience #ServiceDesign #UsagePolicies

5. Functional Requirements for a Rate-Limiting Service

   - Set maximum request rates with configurable time intervals
   - Support for multiple rate limits per endpoint
   - Logging and visibility of rate-limited events
   - Considerations for storage and retention of request data

   #FunctionalRequirements #ServiceConfiguration #Logging

6. Non-Functional Requirements

   6.1 Scalability:

   - Handle billions of daily requests
   - Efficient storage management for user data

     6.2 Performance:

   - Low latency responses (e.g., P99 of 100ms)
   - Quick decision-making for rate limiting

     6.3 Complexity:

   - Simple design for ease of use and maintenance
   - Minimize risk of bugs and outages

     6.4 Security and Privacy:

   - Protect against external and internal threats
   - Ensure data privacy across services

     6.5 Availability and Fault-Tolerance:

   - Balance between uptime and cost considerations
   - Graceful degradation during outages

     6.6 Accuracy and Consistency:

   - Prevent false positives in rate limiting
   - Allow for some flexibility in limit enforcement

   #NonFunctionalRequirements #ServicePerformance #SecurityConsiderations

7. High-Level Architecture Components

   - Frontend/API Gateway: Initial request handling
   - Backend Service: Core rate limiting logic
   - Rules Service: Manages rate limit configurations
   - Storage: For request counts and user data (e.g., Redis, SQL)
   - Logging and Monitoring: For visibility and analytics

   #ArchitectureDesign #SystemComponents #DataStorage

8. Stateful vs. Stateless Approaches

   8.1 Stateless Approach:

   - Uses shared database for request counts
   - Scalable but depends on external storage performance

     8.2 Stateful Approach:

   - Stores counts on individual hosts
   - Requires complex load balancing and synchronization

     8.3 Hybrid Approach:

   - Stores aggregated counts on all hosts
   - Balances performance and simplicity

   #StatefulDesign #StatelessArchitecture #HybridSystems

9. Rate-Limiting Algorithms

   9.1 Token Bucket:

   - Analogy of filling and depleting tokens
   - Simple to implement, memory-efficient

     9.2 Leaky Bucket:

   - Fixed-rate token depletion
   - Smooths out request processing

     9.3 Fixed Window Counter:

   - Counts requests in fixed time intervals
   - Simple but can allow burst traffic

     9.4 Sliding Window Log:

   - Maintains detailed request timestamps
   - Accurate but memory-intensive

     9.5 Sliding Window Counter:

   - Combines fixed windows for smoother limiting
   - Balances accuracy and efficiency

   Table: Comparison of Rate-Limiting Algorithms
   | Algorithm | Pros | Cons |
   |----------------------|------------------------|---------------------------|
   | Token Bucket | Simple, efficient | Sync and cleanup issues |
   | Leaky Bucket | Smooth traffic flow | Slightly inaccurate |
   | Fixed Window Counter | Easy implementation | Allows traffic bursts |
   | Sliding Window Log | High accuracy | Memory intensive |
   | Sliding Window Counter | Balanced approach | Moderate complexity |

   #RateLimitingAlgorithms #TrafficManagement #SystemOptimization

---

References:

1. [Animated Rate Limiting](https://smudge.ai/blog/ratelimit-algorithms)

2. [Rate limiter and its algorithms with illustrations](https://tech.groww.in/rate-limiter-and-its-algorithms-with-illustrations-564455162935)

3. [Rate Limiter for a distributed and scalable system](https://tech.groww.in/rate-limiter-for-a-distributed-and-scalable-system-4a350ee1bb8a)

4. [TLS/SSL Certificate Pinning Explained](https://www.youtube.com/watch?v=3coPpYJgFro)

5. [SSL Pinning Explained](https://www.youtube.com/watch?v=efIPpIYBNTc)

6. [DNS Cache poisoning](https://www.youtube.com/watch?v=7MT1F0O3_Yw)

7. [Rate Limiting at Figma](https://www.figma.com/blog/an-alternative-approach-to-rate-limiting/)
