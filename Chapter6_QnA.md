# Chapter 6 - Common services for functional partitioning - (Q&A)

1. **Q: What's the main job of an API gateway?**
   A: An API gateway acts as a single entry point for client requests. It handles common tasks like security, request routing, and data transformation, simplifying the backend architecture and improving overall system management.
   #APIGateway #Microservices

2. **Q: Name the key security tasks an API gateway handles.**
   A: The main security tasks are:

   - Authentication (verifying user identity)
   - Authorization (checking user permissions)
   - SSL termination (handling encrypted connections)
   - Data encryption (protecting sensitive information)
     #APIGatewaySecurity

3. **Q: How is a service mesh different from an API gateway?**
   A: A service mesh focuses on internal service-to-service communication, while an API gateway handles external client requests. The service mesh uses a sidecar pattern, with a proxy for each service instance, providing more detailed control over inter-service communication.
   #ServiceMesh #Microservices

4. **Q: Why would you use a metadata service?**
   A: A metadata service centralizes shared information, reducing data duplication and improving consistency. It allows services to exchange lightweight IDs instead of full data objects, saving resources and simplifying updates to shared information.
   #MetadataService #DistributedSystems

5. **Q: Why is service discovery important in microservices?**
   A: Service discovery allows services to find and communicate with each other dynamically. It's crucial for automatic detection of new service instances, load balancing, and maintaining system resilience by avoiding unhealthy instances.
   #ServiceDiscovery #Microservices

6. **Q: What are the pros and cons of using GraphQL instead of REST?**
   A: Pros of GraphQL include precise data fetching, reduced over/under-fetching, and easier API evolution. Cons include increased server-side complexity, potential performance issues with complex queries, and more difficult caching. The choice depends on the specific API requirements and use cases.
   #GraphQL #APIDesign #REST

7. **Q: What are the main categories of development frameworks?**
   A: The main categories are:

   - Web development
   - Mobile development (Android and iOS)
   - Backend development
   - PC/Desktop development
     Each category has its own set of frameworks tailored to specific platforms and use cases.
     #DevelopmentFrameworks

8. **Q: Name some popular frameworks for browser app development.**
   A: Popular browser app frameworks include React, Vue.js, and Angular. These frameworks allow developers to build complex, interactive web applications using JavaScript or TypeScript.
   #BrowserAppFrameworks

9. **Q: What's the difference between native and cross-platform mobile development?**
   A: Native development uses platform-specific languages (like Swift for iOS or Kotlin for Android), while cross-platform frameworks like React Native or Flutter allow developers to write code once and deploy on multiple platforms.
   #MobileDevelopment

10. **Q: What are some key server-side frameworks and their primary languages?**
    A: Key server-side frameworks include:

    - Express (Node.js/JavaScript)
    - Django and Flask (Python)
    - Spring Boot (Java)
    - Ruby on Rails (Ruby)
      These frameworks facilitate backend development with different language preferences and feature sets.
      #ServerSideFrameworks

11. **Q: What is the role of full-stack frameworks in development?**
    A: Full-stack frameworks like Ruby on Rails, Django, or MEAN stack (MongoDB, Express, Angular, Node.js) provide tools for both frontend and backend development. They aim to streamline the development process by offering a unified approach to building web applications.
    #FullStackFrameworks

12. **Q: How do RPC frameworks differ from REST frameworks in backend development?**
    A: RPC (Remote Procedure Call) frameworks like gRPC focus on defining services in terms of methods that can be called remotely, often with strict typing. REST frameworks, on the other hand, revolve around resources and HTTP methods. RPC can be more efficient for complex operations, while REST is often simpler and more widely understood.
    #BackendFrameworks #RPC #REST

---

1. Q: What's the main job of an API gateway?
   A: An API gateway acts as a single entry point for client requests. It handles common tasks like security, request routing, and data transformation, simplifying the backend architecture and improving overall system management.
   #APIGateway #Microservices

2. Q: Name the key security tasks an API gateway handles.
   A: The main security tasks are:

   - Authentication (verifying user identity)
   - Authorization (checking user permissions)
   - SSL termination (handling encrypted connections)
   - Data encryption (protecting sensitive information)
     #APIGatewaySecurity

3. Q: How is a service mesh different from an API gateway?
   A: A service mesh focuses on internal service-to-service communication, while an API gateway handles external client requests. The service mesh uses a sidecar pattern, with a proxy for each service instance, providing more detailed control over inter-service communication.
   #ServiceMesh #Microservices

4. Q: Why would you use a metadata service?
   A: A metadata service centralizes shared information, reducing data duplication and improving consistency. It allows services to exchange lightweight IDs instead of full data objects, saving resources and simplifying updates to shared information.
   #MetadataService #DistributedSystems

5. Q: Why is service discovery important in microservices?
   A: Service discovery allows services to find and communicate with each other dynamically. It's crucial for automatic detection of new service instances, load balancing, and maintaining system resilience by avoiding unhealthy instances.
   #ServiceDiscovery #Microservices

6. Q: What are the pros and cons of using GraphQL instead of REST?
   A: Pros of GraphQL include precise data fetching, reduced over/under-fetching, and easier API evolution. Cons include increased server-side complexity, potential performance issues with complex queries, and more difficult caching. The choice depends on the specific API requirements and use cases.
   #GraphQL #APIDesign #REST

7. Q: What are the main categories of development frameworks?
   A: The main categories are:

   - Web development
   - Mobile development (Android and iOS)
   - Backend development
   - PC/Desktop development
     Each category has its own set of frameworks tailored to specific platforms and use cases.
     #DevelopmentFrameworks

8. Q: Name some popular frameworks for browser app development.
   A: Popular browser app frameworks include React, Vue.js, and Angular. These frameworks allow developers to build complex, interactive web applications using JavaScript or TypeScript.
   #BrowserAppFrameworks

9. Q: What's the difference between native and cross-platform mobile development?
   A: Native development uses platform-specific languages (like Swift for iOS or Kotlin for Android), while cross-platform frameworks like React Native or Flutter allow developers to write code once and deploy on multiple platforms.
   #MobileDevelopment

10. Q: What are some key server-side frameworks and their primary languages?
    A: Key server-side frameworks include:

    - Express (Node.js/JavaScript)
    - Django and Flask (Python)
    - Spring Boot (Java)
    - Ruby on Rails (Ruby)
      These frameworks facilitate backend development with different language preferences and feature sets.
      #ServerSideFrameworks

11. Q: What is the role of full-stack frameworks in development?
    A: Full-stack frameworks like Ruby on Rails, Django, or MEAN stack (MongoDB, Express, Angular, Node.js) provide tools for both frontend and backend development. They aim to streamline the development process by offering a unified approach to building web applications.
    #FullStackFrameworks

12. Q: How do RPC frameworks differ from REST frameworks in backend development?
    A: RPC (Remote Procedure Call) frameworks like gRPC focus on defining services in terms of methods that can be called remotely, often with strict typing. REST frameworks, on the other hand, revolve around resources and HTTP methods. RPC can be more efficient for complex operations, while REST is often simpler and more widely understood.
    #BackendFrameworks #RPC #REST
