# Chapter 1 - A walkthrough of system design concepts (Summary)

Study Notes:

1. The scalability of a service refers to the ability to easily and cost-effectively vary resources allocated to it to serve changes in load, both in terms of increasing or decreasing user numbers and/or requests to the system. #scalability

2. The initial system design of the "Beigel" app consists of a browser app, iOS app, Android app, a stateless backend service, and a SQL database. This is a common starting point for many apps. #architecture

3. As the user base and request load grows, the system can be scaled using GeoDNS to route clients to the data center closest to them, improving latency and reliability. #GeoDNS

4. A Redis cache service is added to serve cached requests and reduce the load on the backend service. #caching

5. A content distribution network (CDN) is used to host static content like JavaScript, CSS, and images, improving latency, throughput, reliability, and cost. #CDN

6. The frontend and backend services are horizontally scalable, allowing more hosts to be provisioned to support larger request loads. #horizontalScaling

7. Continuous integration and continuous deployment (CI/CD) practices are adopted, along with tools like Docker, Kubernetes, Ansible, and Terraform, to manage the infrastructure and deployment of the services. #CI/CD

8. Gradual rollouts and rollbacks are used to deploy new builds to production, with the ability to roll back if any issues are detected. #deployment

9. Experimentation, such as A/B testing and multi-armed bandit, is used to gradually roll out new features and measure their impact on user behavior. #experimentation

10. Functional partitioning is used to separate various functions into different services or hosts, and shared services are introduced to handle cross-cutting concerns like search, logging, monitoring, and alerting. #functionalPartitioning #sharedServices

11. An API gateway is introduced to centralize cross-cutting concerns like authorization, authentication, rate limiting, and analytics, but this architecture has drawbacks like added latency and complexity. #APIGateway

12. A service mesh, using a sidecar pattern, is introduced as a solution to the drawbacks of the API gateway architecture. #serviceMesh

13. Command Query Responsibility Segregation (CQRS) is a microservices pattern where command/write operations and query/read operations are functionally partitioned onto separate services. #CQRS #microservices

14. Batch and streaming extract, transform, and load (ETL) are used to handle asynchronous data processing tasks that do not require immediate processing. #ETL

15. Other common services are introduced, such as:

| Service                                       | Description                                               |
| --------------------------------------------- | --------------------------------------------------------- |
| Customer/external user credentials management | For external user authentication and authorization        |
| Various storage services                      | Including database services with different requirements   |
| Asynchronous processing                       | To handle traffic spikes and efficiently utilize hardware |
| Notebooks service                             | For analytics, machine learning, and experimentation      |
| Internal search                               | Including subproblems like autocomplete/typeahead         |
| Privacy compliance                            | To safeguard user privacy                                 |
| Fraud detection                               | To detect and prevent fraud                               |

#architecture

16. The decision to use cloud services versus bare metal hosts is discussed, with the cloud offering benefits like simplicity of setup, cost advantages, better support and quality, and easier upgrades, but also potential drawbacks like vendor lock-in and lack of ownership over data and security. #cloudvsbareMetal

17. Serverless Function as a Service (FaaS) is introduced as a potential solution for infrequently used or non-latency-sensitive functions, but the portability of FaaS is still a topic of debate. #FaaS
