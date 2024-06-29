# Chapter 6 - Common services for functional partitioning (Summary)

1. **Functional partitioning**: A scalability technique that separates specific functions from the backend to run on dedicated clusters.

2. **API Gateway**: A lightweight web service that centralizes common functionalities across various services. Key functions include:

   - **Security**: Authentication, authorization, SSL termination, and data encryption
   - **Error-checking**: Request validation and deduplication
   - **Performance optimization**: Caching, rate limiting, and request dispatching
   - **Logging and analytics**

3. **Service Mesh / Sidecar Pattern**: Addresses API gateway disadvantages by reducing latency and improving scaling. Components include:

   - Control plane
   - Data plane
   - Observability plane

4. **Metadata Service**: Stores information used by multiple system components, improving consistency and reducing duplicate data.

5. **Service Discovery**: Allows clients to identify available service hosts, crucial for managing multiple services in a microservices architecture.

6. **Development Frameworks**:

   - **Browser App**: React, Vue.js, Angular
   - **Server-Side**: Express (Node.js), Flask (Python), Spring Boot (Java)
   - **Mobile**: Native (Android: Kotlin/Java, iOS: Swift/Objective-C) and cross-platform (React Native, Flutter)
   - **Backend**: gRPC, REST frameworks, full-stack frameworks

7. **Progressive Web Apps (PWA)**: Provide desktop and mobile-like experiences using web technologies.

8. **GraphQL**: Allows precise data requests but comes with challenges in security, performance optimization, and schema definition.

9. **Web Server Role** (e.g., Node.js): Acts as an intermediary between browser apps and backend services, optimizing data transmission and processing.

10. **Cross-platform Development**: Frameworks like React Native, Flutter, and Ionic aim to reduce duplicate work across different platforms.

Simplified Mindmap

API Gateway and Service Mesh
|
+-- API Gateway
| +-- Functionalities
| | +-- Security
| | +-- Error-checking
| | +-- Performance and Availability
| | +-- Logging and Analytics
| +-- Benefits
| +-- Challenges
|
+-- Service Mesh
| +-- Sidecar Pattern
| +-- Components
| | +-- Control Plane
| | +-- Data Plane
| | +-- Observability Plane
| +-- Benefits
|
+-- Metadata Service
| +-- Purpose
| +-- Benefits
| +-- Tradeoffs
|
+-- Service Discovery
| +-- Concept
| +-- Service Registry
| +-- Importance in Microservices
|
+-- Development Frameworks
+-- Web Development
+-- Mobile Development
+-- Backend Development
+-- Desktop Development

References:

1. [Choose Boring Technology](https://mcfunley.com/choose-boring-technology)
2. [Spoken word version of the essay - Choose Boring Technology](https://boringtechnology.club/)
