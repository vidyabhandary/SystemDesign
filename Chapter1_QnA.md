# Chapter 1 (Q&A) - A walkthrough of system design concepts

Questions and Answers:

1. What does the term "scalability" refer to in the context of a service? #scalability
   Answer: The scalability of a service refers to the ability to easily and cost-effectively vary the resources allocated to it in order to handle changes in load, such as increases or decreases in the number of users or requests to the system.

2. What was the initial system design of the "Beigel" app? #architecture
   Answer: The initial system design of the "Beigel" app consisted of a browser app, iOS app, Android app, a stateless backend service, and a SQL database. This is a common starting point for many apps.

3. How can the system be scaled as the user base and request load grows? #GeoDNS
   Answer: As the user base and request load grows, the system can be scaled using GeoDNS to route clients to the data center closest to them. This improves latency and reliability by serving users from the nearest available resources.

4. What service was added to serve cached requests and reduce the load on the backend service? #caching
   Answer: A Redis cache service was added to serve cached requests and reduce the load on the backend service.

5. How does using a content distribution network (CDN) benefit the system? #CDN
   Answer: Using a CDN to host static content like JavaScript, CSS, and images improves latency, throughput, reliability, and cost. The CDN stores copies of the static files in various data centers, allowing users to download them from the geographically closest data center.

6. What characteristic of the frontend and backend services allows the system to be scaled by provisioning more hosts? #horizontalScaling
   Answer: The frontend and backend services are horizontally scalable, meaning that more hosts can be provisioned to support larger request loads without requiring changes to the source code.

7. What practices and tools were adopted to manage the infrastructure and deployment of the services? #CI/CD
   Answer: Continuous integration and continuous deployment (CI/CD) practices were adopted, along with tools like Docker, Kubernetes, Ansible, and Terraform, to manage the infrastructure and deployment of the services.

8. What approaches are used to deploy new builds to production and handle issues that may arise? #deployment
   Answer: Gradual rollouts and rollbacks are used to deploy new builds to production, with the ability to roll back if any issues are detected, such as bugs, crashes, increased latency, or user churn.

9. How is experimentation used to measure the impact of new features on user behavior? #experimentation
   Answer: Experimentation, such as A/B testing and multi-armed bandit, is used to gradually roll out new features to a subset of users and measure their impact on user behavior, rather than deploying changes to all users at once.

10. How is functional partitioning used, and what are the shared services introduced? #functionalPartitioning #sharedServices
    Answer: Functional partitioning is used to separate various functions into different services or hosts. Shared services are introduced to handle cross-cutting concerns, such as search, logging, monitoring, and alerting, rather than having each service implement these features individually.

11. What are the benefits and drawbacks of using an API gateway? #APIGateway
    Answer: An API gateway is introduced to centralize cross-cutting concerns like authorization, authentication, rate limiting, and analytics. However, this architecture has drawbacks like added latency and complexity.

12. How does a service mesh, using a sidecar pattern, address the drawbacks of the API gateway architecture? #serviceMesh
    Answer: A service mesh, using a sidecar pattern, is introduced as a solution to the drawbacks of the API gateway architecture. In this approach, each host of each service runs a sidecar proxy, allowing service requests and responses to be routed through the sidecar without network latency.

13. What is the purpose of the Command Query Responsibility Segregation (CQRS) pattern, and how is it used? #CQRS #microservices
    Answer: CQRS is a microservices pattern where command/write operations and query/read operations are functionally partitioned onto separate services. This introduces complexity but can provide lower latency, better scalability, and easier maintenance and use, as the write and read services can be scaled separately.

14. When are batch and streaming extract, transform, and load (ETL) techniques used, and what are the benefits? #ETL
    Answer: Batch and streaming ETL are used to handle asynchronous data processing tasks that do not require immediate processing, such as large database queries or writes that do not need to be executed immediately. This can help handle unpredictable traffic spikes and efficiently utilize hardware resources.

15. What are some of the other common services introduced in the system? #architecture
    Answer: Other common services introduced in the system include:

    - Customer/external user credentials management for authentication and authorization
    - Various storage services, including database services with different requirements
    - Asynchronous processing to handle traffic spikes and efficiently utilize hardware
    - Notebooks service for analytics, machine learning, and experimentation
    - Internal search, including subproblems like autocomplete/typeahead
    - Privacy compliance services to safeguard user privacy
    - Fraud detection services to detect and prevent fraud

16. What are the key considerations in the decision to use cloud services versus bare metal hosts? #cloudvsbareMetal
    Answer: The decision to use cloud services versus bare metal hosts involves weighing the benefits and drawbacks of each approach. Cloud services offer benefits like simplicity of setup, cost advantages, better support and quality, and easier upgrades, but also potential drawbacks like vendor lock-in and lack of ownership over data and security. Bare metal hosts provide more control but require more engineering effort to set up and maintain the necessary infrastructure and services.

17. How is serverless Function as a Service (FaaS) introduced as a potential solution, and what are the considerations around its portability? #FaaS
    Answer: Serverless Function as a Service (FaaS) is introduced as a potential solution for infrequently used or non-latency-sensitive functions, where functions are only executed when needed, reducing the need for continuously running hosts. However, the portability of FaaS is still a topic of debate, as migrating between different FaaS platforms can be challenging, and the overhead of the FaaS model may become expensive at scale compared to bare metal solutions.
